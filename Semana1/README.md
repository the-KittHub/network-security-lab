# Semana 1 - Infraestructura Base 

Durante esta primera etapa se construyó la infraestructura inicial del laboratorio, estableciendo la base sobre la cual se implementarán los servicios y mecanismos de seguridad en las siguientes semanas. 

El objetivo fue diseñar una red segmentada, habilitar la comunicación entre VLANs y automatizar la asignación de direcciones IP mediante un servidor DHCP dedicado. 

--- 

## 📌Objetivos de la semana 
- Diseñar la topología del laboratorio. 
- Implementar la segmentación mediante VLANs. 
- Configurar el enrutamiento Inter-VLAN (Router-on-a-Stick). 
- Desplegar un servidor Ubuntu Server para brindar el servicio DHCP. 
- Verificar la conectividad entre dispositivos y la asignación automática de direcciones IP. 

--- 

## Tecnologías utilizadas 
- Cisco IOS 
- GNS3 
- VMware 
- Ubuntu Server 24.04 LTS 
- ISC DHCP Server 
- IEEE 802.1Q (Trunking)
- DHCP Relay (`ip helper-address`) 

--- 

## 📋Etapas de implementación 

### 01 - Diseño de la red 
Se definió la topología inicial del laboratorio, estableciendo la segmentación mediante VLANs y la función de cada segmento de la red. 
>📄 [Ver documentación](01-diseño-de-red.md) 

--- 

### 02 - Capa 2 
Implementación de la infraestructura de switching mediante la creación de VLANs y la configuración de enlaces Trunk para permitir su propagación entre los switches. 
>📄 [Ver documentación](02-capa2.md) 

--- 

### 03 - Inter-VLAN Routing 
Configuración del Router-on-a-Stick mediante subinterfaces 802.1Q para permitir la comunicación entre las distintas VLANs y verificación de la conectividad con el servidor. 
>📄 [Ver documentación](03-inter-vlan-routing.md) 

--- 
### 04 - Servidor DHCP 
Despliegue de un servidor Ubuntu Server, configuración del servicio ISC DHCP Server, implementación de DHCP Relay y validación de la asignación automática de direcciones IP. 
>📄 [Ver documentación](04-servidor-dhcp.md) 

--- 

## Resultado de la semana 
- ✅ Topología completamente implementada en GNS3. 
- ✅ Red segmentada mediante VLANs. 
- ✅ Enrutamiento Inter-VLAN operativo. 
- ✅ Servidor DHCP dedicado sobre Ubuntu Server.
- ✅ Asignación automática de direcciones IP. 
- ✅ Infraestructura preparada para incorporar nuevos servicios y mecanismos de seguridad. 

--- 
## ⏩Próximos pasos 
Durante la siguiente etapa se ampliará la infraestructura incorporando nuevos servicios de red y conectividad, preparando el laboratorio para la implementación posterior de políticas de seguridad y hardening. 

## Habilidades desarrolladas 
Durante esta etapa se pusieron en práctica los siguientes conceptos: 
- ✔️ Diseño de redes empresariales. 
- ✔️ Segmentación mediante VLANs. 
- ✔️ Configuración de enlaces Trunk. 
- ✔️ Router-on-a-Stick. 
- ✔️ Administración básica de Ubuntu Server. 
- ✔️ Configuración de servicios DHCP. 
- ✔️ Resolución de problemas mediante herramientas de troubleshooting (ARP, tablas MAC, CDP, ICMP y DHCP). 



