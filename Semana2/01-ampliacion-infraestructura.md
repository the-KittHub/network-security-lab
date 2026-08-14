# 01 - Ampliación de infraestructura

##  📌Objetivo

Durante esta etapa se amplió la topología inicial del laboratorio para incorporar los dispositivos y segmentos necesarios para las siguientes fases del proyecto.

Se agregaron: dispositivos de usuarios, conectividad inalámbrica, nuevos segmentos de servidores, una red dedicada de Management  y se configuró el router externo que representa la conexión con un ISP.

La ampliación busca acercarse más a una infraestructura empresarial más completa y dejar preparada la topología para la incorporación posterior de servicios de administración y mecanismos de seguridad.

---
### Evolución de la topología

La siguiente comparación muestra el cambio entre la infraestructura inicial y la topología ampliada de esta segunda etapa.

<img src="imagenes/comparacion-topologias.png " alt="comaparacion" width="80%">

La ampliación permite separar de forma más clara los diferentes tipos de dispositivos y deja preparados los segmentos que se utilizarán en las siguientes implementaciones.

---
## Implementación / configuración

`SW1-CORE` se mantiene como punto central de distribución y separa los dispositivos según su función.

Las VLANs utilizadas continúan siendo:

| VLAN | Nombre     | Función                                            |
| ---- | ---------- | -------------------------------------------------- |
| 10   | ADMIN      | Equipos del área administrativa                    |
| 20   | IT         | Equipos del área de IT                             |
| 30   | GUEST      | Dispositivos conectados a la red WiFi de invitados |
| 50   | SERVERS    | Servidores de infraestructura y seguridad          |
| 99   | MANAGEMENT | Administración de dispositivos de red              |

Durante esta ampliación se incorporaron:

* nuevos endpoints en el segmento de Administración;
* un Access Point junto con notebook y smartphone para representar la red Guest;
* 1 servidor de seguridad dentro de la VLAN 50;
* el segmento Management;
* configuración en`R2-ISP` para ser utilizado como router externo y simular la salida de la red hacia un ISP.

El enlace entre `R1-EDGE` y `SW1-CORE` continúa funcionando como trunk 802.1Q para transportar las VLANs internas del laboratorio.

En los enlaces hacia los switches de acceso se mantuvieron únicamente las VLANs necesarias para cada segmento junto con la VLAN 99 de administración.

Por ejemplo, para `SW3-IT`:

```cisco
interface GigabitEthernet0/0
 switchport mode trunk
 switchport trunk allowed vlan 20,99
```

Siguiendo el mismo criterio:

```text
SW2-ADMIN    → VLAN 10,99
SW3-IT       → VLAN 20,99
SW4-GUEST    → VLAN 30,99
SW5-SERVERS  → VLAN 50,99
SW6-MANAGEMENT → VLAN 99
```

---
## Verificación

Para verificar que las VLANs necesarias continuarán siendo transportadas correctamente :

```cisco
show vlan brief
```
y:

```cisco
show interfaces trunk
```

De esta forma se comprobó que cada switch mantuviera activa su VLAN correspondiente y que la VLAN 99 pudiera atravesar los enlaces trunk necesarios.

---
## 🏁Resultado

El laboratorio pasó de una infraestructura base orientada principalmente a probar segmentación y conectividad a una topología más completa y preparada para incorporar servicios de administración y seguridad.

La nueva infraestructura conserva la separación lógica mediante VLANs y suma los componentes necesarios para continuar con:

* administración centralizada mediante VLAN 99;
* resolución DNS interna;
* simulación de conectividad hacia una red externa;
* implementación posterior de controles de acceso, hardening y servicios de monitoreo.

Con esta ampliación queda definida la base física y lógica sobre la que se desarrollará el resto de la Semana 2.

## ⏩Próximos pasos 

En la siguiente etapa se implementará VLAN 99. [Ir a la siguiente sección](02-red-management.md)
