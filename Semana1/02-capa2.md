# Implementación de la Capa 2 

## 📌Objetivo 
Implementar la segmentación lógica de la red mediante VLANs y enlaces troncales. 

## Tabla de VLANs 
<img src="images/sw1-vlan.png " alt="sw1-vlan" width="50%">    

## Configuración de enlaces Trunk 
Captura del Switch1-Core donde se configuran las interfaces con sus respectivas VLAN

<img src="images/config-puertos trunk.png " alt="config-puertos" width="50%">   

Verificación de puertos trunk

<img src="images/sw1-sh-int trunk.png " alt="sw1-sh-int" width="50%">                  

## 🏁Resultado 

Todas las VLANs quedaron propagadas correctamente entre los switches mediante enlaces troncales 802.1Q. 

## ⏩Próximos pasos 

En la siguiente etapa se implementará RoaS. 
[Ir a la siguiente sección](03-inter-vlan-routing.md) 
