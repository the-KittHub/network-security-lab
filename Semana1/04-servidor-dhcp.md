# Servidor DHCP

¿Por qué elegí un servidor Ubuntu en lugar del DHCP del router? 

Como se mencionó al inicio del proyecto, la idea es acercarse lo máximo posible a una infraestructura organizacional, por lo que decidí crear una nueva VM que corra Ubuntu Server para dar el servicio DHCP en lugar de utilizar el servicio integrado en routers Cisco.
Esto permite separar funciones, facilitar futuras ampliaciones (DNS, Syslog, etc.) y trabajar con servicios más similares a los utilizados en entornos productivos. 

## 📌Objetivo
Instalar y configurar un servidor DHCP dedicado capaz de asignar direcciones IP dinámicamente a todas las VLANs del laboratorio. 

## Configuración de Netplan

<img src="images/dhcp-netplan.png " alt="dhcp-netplan" width="25%">  

## Definición de los pools

<img src="images/dhcp-server-pool.png " alt="dhcp-server-pool" width="30%">  

## Estado del servicio

<img src="images/dhcp-server-status.png " alt="dhcp-server-status" width="60%">   

## Del lado del router
Se configuró DHCP Relay (ip helper-address) sobre cada subinterfaz para reenviar las solicitudes DHCP hacia el servidor. 
<img src="images/r1-helper-address.png " alt="r1-helper-address.png" width="40%">

## ✅Verificación 
<img src="images/ip-dhcp.gif " alt="ip-dhcp.gif" width="60%"> <img src="images/ip-dhcp.png " alt="ip-dhcp.png" width="60%">

La PC obtiene automáticamente: 
- Dirección IP 
- Máscara 
- Gateway predeterminado 
confirmando el correcto funcionamiento del servidor DHCP. 

## 🏁 Resultado 
El laboratorio ya cuenta con asignación dinámica de direcciones IP para todas las VLANs mediante un servidor dedicado Ubuntu. 

## Topología al final de la etapa
<img src="images/topologia-lab-final-etapa.png " alt="topologia-lab-final-etapa.png " width="50%"> 
