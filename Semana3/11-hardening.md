# 11 - Hardening

Se aplicaron medidas de hardening sobre los routers, switches y puertos de acceso.

🎯 El objetivo fue **reducir la superficie de ataque** de los dispositivos, limitar servicios innecesarios, proteger las sesiones administrativas y aplicar **controles de seguridad** sobre las interfaces utilizadas por los hosts finales.

---

## Hardening de dispositivos

Se definió un baseline de seguridad para los dispositivos de infraestructura.

Entre las principales medidas implementadas se encuentran:

* Protección del acceso al modo privilegiado.
* Cifrado de contraseñas almacenadas en la configuración.
* Deshabilitación de servicios de administración no utilizados.
* Limitación del tiempo de las sesiones administrativas.
* Registro de intentos de autenticación.
* Implementación de banners de advertencia.

---

## Protección de credenciales

Se configuró una contraseña para el acceso al modo privilegiado mediante:

```cisco
enable secret <CONTRASEÑA>
```

También se habilitó:

```cisco
service password-encryption
```

Esto evita que determinadas contraseñas aparezcan directamente en texto plano dentro de la configuración.

Para las credenciales administrativas principales se priorizó el uso de `secret`, evitando mecanismos de almacenamiento de contraseñas reversibles.

<img src="images/12-secret-pswd.png " alt=" 12-secret-pswd" width="60%">

---

## Deshabilitación de servicios innecesarios

Los servicios HTTP y HTTPS integrados en Cisco IOS fueron deshabilitados al no ser utilizados para la administración de los dispositivos, disminuyendo la superficie de ataque de la infraestructura.

```cisco
no ip http server
no ip http secure-server
```

---

## Timeout de sesiones administrativas

Se configuró un tiempo máximo de inactividad de cinco minutos para las sesiones de consola y acceso remoto:

```cisco
line console 0
 exec-timeout 5 0
 logging synchronous
```

```cisco
line vty 0 4
 exec-timeout 5 0
```
(La configuración completa del acceso remoto mediante SSH y la restricción de las líneas VTY se encuentra documentada en la etapa anterior).

---

## Logging de autenticaciones

Se habilitó el registro de intentos de autenticación exitosos y fallidos:

```cisco
login on-failure log
login on-success log
```

Estos eventos podrán ser centralizados posteriormente mediante un servidor Syslog, mejorando la trazabilidad de los accesos administrativos.

---

## Banner de acceso

Se configuró un banner de advertencia en los dispositivos de infraestructura:

<img src="images/13-banner-acceso.png " alt=" 13-banner-acceso" width="60%">

El mensaje informa que el acceso se encuentra restringido y que la actividad administrativa puede ser registrada.

---

# Hardening de Capa 2

Además del endurecimiento de routers y switches, se aplicaron medidas de seguridad sobre los puertos utilizados por dispositivos finales.

La configuración implementada como baseline fue:

```cisco
interface GigabitEthernet0/X
 switchport mode access
 switchport access vlan <VLAN>

 spanning-tree portfast
 spanning-tree bpduguard enable

 switchport port-security
 switchport port-security maximum 1
 switchport port-security mac-address sticky
 switchport port-security violation restrict
```

---

## Puertos de acceso

Los puertos conectados a hosts finales fueron configurados explícitamente como puertos Access:

```cisco
switchport mode access
switchport access vlan <VLAN>
```

Esto permite mantener cada interfaz asociada a la VLAN correspondiente y evita depender de mecanismos de negociación automática.

---

## PortFast y BPDU Guard

En los puertos destinados exclusivamente a hosts finales se habilitó `PortFast` y `BPDU Guard` para que pasen a estado de reenvío rápidamente pero manteniendo una protección ante una conexión no autorizada:

```cisco
spanning-tree portfast
spanning-tree bpduguard enable
```
---

## Port Security

Se implementó Port Security para limitar las direcciones MAC permitidas en los puertos de acceso:

```cisco
switchport port-security
switchport port-security maximum 1
switchport port-security mac-address sticky
switchport port-security violation restrict
```

Cada puerto protegido puede aprender automáticamente una única dirección MAC legítima mediante Sticky MAC.

En `SW2-ADMIN`, por ejemplo, `Gi0/1` aprendió la MAC correspondiente a `Host-admin-1`:

```text
0242.e327.c300
```

La dirección fue registrada como:

```text
SecureSticky
```

y quedó asociada al puerto.

<img src="images/14-stiky-mac.png " alt=" 14-stiky-mac" width="60%">

---

## Modo de violación Restrict

Se seleccionó el modo:

```cisco
switchport port-security violation restrict
```

Cuando se detecta tráfico proveniente de una MAC no autorizada:

* El puerto permanece operativo.
* El tráfico de la MAC desconocida es descartado.
* Se incrementa el contador de violaciones.
* Se genera un evento de Port Security.

Esto permite mantener disponible el puerto para el equipo legítimo mientras se bloquea el tráfico generado por dispositivos no autorizados.

---

## Validación de Port Security

Para comprobar el funcionamiento del control se realizó una prueba sobre `Gi0/1` de `SW2-ADMIN`.

Inicialmente el puerto había aprendido mediante Sticky MAC la dirección del host autorizado:

```text
0242.e327.c300
```

Posteriormente se desconectó temporalmente el dispositivo y se conectó un segundo host con una MAC diferente:

```text
0242.2ca2.8900
```

<img src="images/15-host-no-autorizado.png " alt=" 15-host-no-autorizado" width="60%">

Al generar tráfico desde el nuevo dispositivo, Port Security detectó la violación.

La verificación mostró:

```text
Port Security              : Enabled
Port Status                : Secure-up
Violation Mode             : Restrict
Maximum MAC Addresses      : 1
Sticky MAC Addresses       : 1
Security Violation Count   : 38
```

El puerto permaneció en estado `Secure-up`, mientras que el tráfico proveniente de la MAC no autorizada fue descartado.


<img src="images/16-sh-port-security.png " alt=" 16-sh-port-security" width="50%"> <img src="images/16-sh-port-security2.png " alt=" 16-sh-port-security2" width="50%">

IOS también generó el siguiente evento:

```text
%PORT_SECURITY-2-PSECURE_VIOLATION:
Security violation occurred, caused by MAC address
0242.2ca2.8900 on port GigabitEthernet0/1.
```
img 17
<img src="images/17-psecure-violation.png " alt=" 17-psecure-violation" width="70%"> 

La tabla de direcciones seguras continuó conservando únicamente la MAC legítima:

```text
Vlan    Mac Address       Type          Ports
----    -----------       ----          -----
10      0242.e327.c300    SecureSticky  Gi0/1
```

Esto confirmó que el dispositivo no autorizado no reemplazó la dirección previamente aprendida.

---

## Puertos no utilizados

Las interfaces que no se encuentran actualmente en uso fueron deshabilitadas administrativamente para evitar que un dispositivo pueda conectarse directamente a un puerto disponible 

```cisco
interface range <PUERTOS-NO-UTILIZADOS>
 description UNUSED
 shutdown
```

---

## Consideraciones según el tipo de dispositivo

Port Security fue aplicado sobre los puertos conectados directamente a hosts finales.

En `SW4-GUEST`, el puerto correspondiente al Access Point fue excluido de esta política, ya que conceptualmente puede representar múltiples clientes utilizando diferentes direcciones MAC.

También se mantuvo una interfaz disponible en el segmento de Management para permitir futuras pruebas controladas dentro del laboratorio.

---

## Resultado

La aplicación del baseline de hardening permitió reducir la superficie de ataque de los dispositivos y proteger los puertos destinados a usuarios finales.

Como resultado:

* ✅ Acceso privilegiado protegido mediante `enable secret`.
* ✅ Contraseñas almacenadas protegidas frente a exposición directa en texto plano.
* ✅ Servicios HTTP y HTTPS innecesarios deshabilitados.
* ✅ Sesiones administrativas limitadas mediante timeouts.
* ✅ Registro de autenticaciones exitosas y fallidas habilitado.
* ✅ Banner de advertencia configurado.
* ✅ Puertos de usuario configurados explícitamente como Access.
* ✅ PortFast y BPDU Guard habilitados en puertos destinados a hosts finales.
* ✅ Port Security implementado mediante Sticky MAC.
* ✅ Una única dirección MAC permitida en los puertos protegidos.
* ✅ Tráfico proveniente de MACs no autorizadas bloqueado mediante modo `restrict`.
* ✅ Violaciones de Port Security registradas correctamente.
* ✅ Puertos no utilizados administrativamente deshabilitados.

La infraestructura queda preparada para incorporar mecanismos de monitoreo y administración centralizada mediante **NTP, Syslog, SNMP y AAA**.
