# Diseño de la red

## Criterios de segmentación  
Diseñar una topología de red empresarial escalable y segmentada que permita implementar progresivamente distintos servicios y mecanismos de seguridad. 

## Topología propuesta 

<img src="images/topologia-lab-inicio-semana.png " alt="topologia" width="40%">

## Segmentación mediante VLANs 

| VLAN | Nombre | Función | 
|------|---------|---------| 
|10|ADMIN|Equipos administrativos| 
|20|IT|Personal de IT| 
|30|GUEST|Red para invitados| 
|50|SERVERS|Servicios internos (DHCP, DNS, etc.)| 
|99|MANAGEMENT|Administración de dispositivos|

## Decisiones de diseño 
- Switch Core encargado de concentrar la red. 
- Router-on-a-Stick para el enrutamiento entre VLANs. 
- Servidor dedicado para los servicios de infraestructura. 
- Topología en estrella para facilitar el crecimiento y la administración. 

## ⏩Próximos pasos 

En la siguiente etapa se implementará la segmentación mediante VLANs sobre los switches.
[Ir a la siguiente sección](02-capa2.md) 

