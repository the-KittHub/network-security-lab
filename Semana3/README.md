# Semana 3 - Seguridad y Hardening

Durante esta etapa se implementaron mecanismos de seguridad orientados al control de acceso, la administración segura de los dispositivos y el endurecimiento de la infraestructura de red.

El objetivo fue comenzar a **aplicar políticas de seguridad** sobre la red ya segmentada, restringiendo el tráfico entre determinados segmentos, limitando el acceso administrativo y protegiendo los puertos de acceso frente a conexiones no autorizadas.

<img src="images/resumen-etapa-3.png " alt="resumen-etapa-3" width="80%">

---

## 🎯Objetivos de la semana

* Implementar políticas de control de acceso mediante ACLs.
* Restringir el acceso de la VLAN Guest a las redes internas.
* Configurar administración remota segura mediante SSH.
* Limitar el acceso administrativo a una estación autorizada.
* Aplicar medidas de hardening sobre routers y switches.
* Implementar Port Security en los puertos de acceso.
* Deshabilitar interfaces y servicios no utilizados.
* Validar el funcionamiento de los controles de seguridad implementados.

---

## 🛠️ Tecnologías utilizadas

* Cisco IOS
* GNS3
* ACLs
* SSHv2
* RSA
* Port Security
* Sticky MAC
* Spanning Tree PortFast
* BPDU Guard
* VLAN de Management
* Logging de autenticaciones

---

## Etapas de implementación

### 09 - ACLs y segmentación segura

Implementación de una ACL extendida para controlar el tráfico originado desde la VLAN Guest, permitiendo únicamente los servicios necesarios y restringiendo el acceso hacia las redes internas del laboratorio.

Se realizaron pruebas de conectividad para validar tanto el tráfico permitido como los accesos bloqueados.

📄 [Ver documentación](09-ACLs-y-segmentación.md)

---

### 10 - SSH y acceso remoto

Configuración de SSHv2 para la administración remota de routers y switches, utilizando autenticación local y claves RSA.

Se incorporó una estación de administración dedicada dentro de la VLAN IT y se implementó una ACL para permitir el acceso administrativo únicamente desde este host autorizado.

📄 [Ver documentación](10-SSH-acceso-remoto.md)

---

### 11 - Hardening

Aplicación de un baseline de hardening sobre los dispositivos de infraestructura y los puertos de acceso.

Se configuraron medidas como Port Security con aprendizaje Sticky MAC, BPDU Guard, PortFast, deshabilitación de puertos no utilizados, timeouts de sesiones administrativas, protección de credenciales y deshabilitación de servicios innecesarios.

También se realizaron pruebas de violación de Port Security para verificar el bloqueo de dispositivos no autorizados.

📄 [Ver documentación](11-hardening.md)

---

## Resultado de la semana

Al finalizar esta etapa el laboratorio cuenta con:

* ✅ VLAN Guest aislada de las redes internas mediante ACLs.
* ✅ Principio de mínimo privilegio en el acceso a servicios.
* ✅ Administración remota mediante SSHv2.
* ✅ Acceso administrativo restringido a una estación autorizada de IT.
* ✅ Telnet y servicios de administración innecesarios deshabilitados.
* ✅ Port Security implementado en puertos de acceso.
* ✅ Aprendizaje automático de direcciones MAC mediante Sticky MAC.
* ✅ BPDU Guard y PortFast configurados en puertos destinados a hosts finales.
* ✅ Puertos no utilizados administrativamente deshabilitados.
* ✅ Registro de intentos de autenticación y violaciones de políticas preparado para su futura centralización.
* ✅ Infraestructura preparada para incorporar mecanismos de monitoreo y administración centralizada.

---

## Próximos pasos

Durante la siguiente etapa se incorporarán servicios orientados al monitoreo, sincronización y administración centralizada de la infraestructura mediante **NTP, Syslog, SNMP y AAA**.

Esto permitirá mejorar la visibilidad sobre los dispositivos de red, centralizar eventos y preparar el laboratorio para una futura integración con herramientas de monitoreo y seguridad.

