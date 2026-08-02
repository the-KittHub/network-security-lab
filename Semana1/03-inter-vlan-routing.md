# Inter-vlan-routing

## 📌Objetivos
Implementar el enrutamiento entre VLANs mediante Router-on-a-Stick y verificar la conectividad entre los distintos segmentos de la red. 

## Subinterfaces 
Se crearon subinterfaces sobre la interfaz física conectada al switch core, asignando una puerta de enlace para cada VLAN. 

<img src="images/router-subinterfaces.png " alt="router-subinterfaces" width="60%">

## ✅Verificación de la configuración
Estado de los enlaces trunk

<img src="images/trunk-database.png " alt="trunk-database" width="60%">    

## Prueba de conectividad
Durante la prueba puede observarse el intercambio ARP previo a la comunicación ICMP, confirmando el correcto funcionamiento del enrutamiento. 
Se envió un ping desde el servidor para verificar la comunicación entre el router y el servidor ubicado en la vlan 50
Además si hubiera algún problema de compatibilidad o conexión entre VMs también podría surgir en este momento 

<img src="images/arp-debug.gif " alt="arp-debug" width="60%">    

Durante la prueba puede observarse el intercambio ARP previo a la comunicación ICMP, confirmando el correcto funcionamiento del enrutamiento. 

## 🏁Resultado 
El router actúa correctamente como gateway de todas las VLANs y existe conectividad con el servidor de infraestructura. 

## ⏩Próximos pasos 

En la siguiente etapa se implementará el servidor DHCP dedicado para asignar direcciones IP de forma automática a cada segmento de red.
[Ir a la siguiente sección](04-servidor-dhcp.md)

