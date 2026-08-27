# 09 - ACLs y Segmentación segura

En esta etapa se implementaron políticas de control de acceso destinadas a limitar la comunicación entre segmentos de la red y proteger el acceso administrativo a los dispositivos de infraestructura.

🎯 Se utilizaron ACLs con dos objetivos diferentes:

* **Restringir el tráfico** originado desde la VLAN Guest hacia las redes internas.
* **Limitar el acceso** remoto por SSH únicamente a una estación administrativa autorizada.

---

## ACL para la VLAN Guest

La VLAN 30 es la destinada a dispositivos invitados, por lo que debe cumplir dos condiciones:
* ➡️  Tener acceso a determinados servicios de infraestructura
* ➡️  No debería poder comunicarse libremente con las redes internas.

Se definió la siguiente política:

| Origen        | Destino / Servicio       | Acción      |
| ------------- | ------------------------ | ----------- |
| VLAN 30 Guest | DHCP                     | ✅ Permitido |
| VLAN 30 Guest | DNS `192.168.50.10`      | ✅ Permitido |
| VLAN 30 Guest | VLAN 10 - Administración | ❌ Bloqueado |
| VLAN 30 Guest | VLAN 20 - IT             | ❌ Bloqueado |
| VLAN 30 Guest | VLAN 50 - Servers        | ❌ Bloqueado |
| VLAN 30 Guest | VLAN 99 - Management     | ❌ Bloqueado |
| VLAN 30 Guest | Otros destinos           | ✅ Permitido |

Excepción de la política: acceso al servidor DNS 

---

## Implementación de GUEST-IN

Se creó una ACL extendida nombrada en `R1-EDGE`.

```cisco
ip access-list extended GUEST-IN
 10 permit udp any eq bootpc any eq bootps
 20 permit udp 192.168.30.0 0.0.0.255 host 192.168.50.10 eq domain
 30 permit tcp 192.168.30.0 0.0.0.255 host 192.168.50.10 eq domain
 40 deny ip 192.168.30.0 0.0.0.255 192.168.10.0 0.0.0.255
 50 deny ip 192.168.30.0 0.0.0.255 192.168.20.0 0.0.0.255
 60 deny ip 192.168.30.0 0.0.0.255 192.168.50.0 0.0.0.255
 70 deny ip 192.168.30.0 0.0.0.255 192.168.99.0 0.0.0.255
 80 permit ip 192.168.30.0 0.0.0.255 any
```

Las ACE fueron numeradas utilizando intervalos de diez para facilitar futuras modificaciones y permitir la incorporación de nuevas reglas, la ACL fue aplicada en dirección de entrada sobre la subinterfaz correspondiente a la VLAN Guest:

```cisco
interface GigabitEthernet0/0.30
 ip access-group GUEST-IN in
```

Al tratarse de una ACL extendida, se ubicó cerca del origen del tráfico que se desea controlar.
<img src="images/01-config-ACL-guest-in.png " alt="01-config-ACL-guest-in" width="60%">

---

## Validación del aislamiento

Para verificar el funcionamiento de la política se generó tráfico desde un host perteneciente a la VLAN 30 hacia distintos segmentos internos.

Como prueba representativa se intentó alcanzar un host de la VLAN 20:

```text
PC-Guest> ping 192.168.20.164

*192.168.30.1 icmp_seq=1 ttl=255
(ICMP type:3, code:13, Communication administratively prohibited)
```

🚫 `Communication administratively prohibited` indica que el tráfico fue rechazado por una política administrativa aplicada en el router y no por una falla de conectividad del destino.

<img src="images/02-ping-desde-vlan-guest.png " alt="02-ping-desde-vlan-guest" width="60%">

La evaluación de la ACL confirmó además que las reglas de denegación registraron coincidencias:

```cisco
R1-EDGE# show ip access-lists GUEST-IN
```

Los contadores de las ACE permitieron comprobar que el tráfico generado durante las pruebas estaba siendo procesado por las reglas correspondientes.

<img src="images/03-comparación-acl.png " alt="03-comparación-acl" width="45%"> <img src="images/03-comparación-acl2.png " alt="03-comparación-acl2" width="50%">

---

## Excepción para DNS

Aunque el acceso general hacia VLAN 50 fue bloqueado, se permitió específicamente el servicio DNS.

Desde PC-Guest se logró resolver:

serv1-infra.kit.enterprise.lab → 192.168.50.10

pero el posterior tráfico ICMP hacia el servidor fue rechazado.

De esta forma, Guest puede utilizar el servicio necesario sin obtener acceso general a la red de servidores.

—
La implementación de GUEST-IN permitió aplicar una política de acceso específica sobre la VLAN Guest.

Como resultado:

* ✅ Los clientes Guest continúan obteniendo configuración mediante DHCP.
* ✅ La resolución DNS hacia el servidor interno permanece disponible.
* ✅ El acceso hacia las VLAN de Administración, IT, Servers y Management queda restringido.
* ✅ El acceso a la VLAN de servidores se limita únicamente al servicio DNS requerido.
* ✅ Los contadores de la ACL permiten verificar las coincidencias generadas durante las pruebas.

## ⏩Próximos pasos 

En la siguiente etapa se implementará SSH. [Ir a la siguiente sección](10-SSH-acceso-remoto.md)
