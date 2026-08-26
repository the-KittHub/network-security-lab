# SSH y acceso remoto

Se implementó un mecanismo de **administración remota segura** para los dispositivos de infraestructura del laboratorio.

🎯 El objetivo fue reemplazar métodos de acceso remoto inseguros por SSHv2 y limitar las conexiones administrativas únicamente a una estación autorizada del equipo de IT.

<img src="images/06-conexion-ssh-it-r1.png" alt="06-conexion-ssh-it-r1" width="50%"> 

---

## Estación de administración

Para centralizar las tareas de administración se incorporó un host Linux dedicado dentro de la VLAN 20 - IT.

```text
IT-Admin
IP: 192.168.20.155
VLAN: 20 - IT
```

La estación fue configurada con una dirección IP estática para mantener una identidad de origen consistente y permitir la aplicación de políticas de acceso específicas sobre los dispositivos de red.

Desde este host se realizan las conexiones SSH hacia routers y switches.

---

## Configuración de SSH

Se configuró SSHv2 en los dispositivos de infraestructura utilizando autenticación local y claves RSA.

En `R1-EDGE` se definió el dominio interno utilizado por el laboratorio:

```cisco
ip domain-name kit.enterprise.lab
```

Se creó un usuario administrativo local:

```cisco
username Katmin privilege 15 secret <CONTRASEÑA>
```
Posteriormente se generaron claves RSA de 2048 bits y se habilitó exclusivamente SSH versión 2:

```cisco
crypto key generate rsa modulus 2048
ip ssh version 2
```

Las líneas VTY fueron configuradas para utilizar autenticación local y aceptar únicamente conexiones SSH:

```cisco
line vty 0 4
 login local
 transport input ssh
```
<img src="images/05-config-ssh-en- r1.png " alt=" 05-config-ssh-en- r1" width="60%"> 

De esta forma, Telnet queda deshabilitado como método de administración remota.

---

## Endurecimiento de las sesiones SSH

También se aplicaron parámetros adicionales sobre las sesiones administrativas:

```cisco
ip ssh time-out 60
ip ssh authentication-retries 3
```

Y sobre las líneas VTY:

```cisco
line vty 0 4
 exec-timeout 5 0
```

Esto limita el tiempo disponible para completar la autenticación y cierra sesiones administrativas que permanezcan inactivas.

---

## Restricción del acceso administrativo

La implementación de SSH por sí sola protege la confidencialidad de la sesión, pero no determina qué equipos están autorizados a intentar conectarse.

Para restringir el plano de administración se creó la ACL estándar `SSH-MANAGEMENT`.

```cisco
ip access-list standard SSH-MANAGEMENT
 10 permit host 192.168.20.155
 20 deny any log
```

La única dirección autorizada corresponde a la estación `IT-Admin`.

La ACL se aplicó directamente sobre las líneas VTY:

```cisco
line vty 0 4
 access-class SSH-MANAGEMENT in
 login local
 transport input ssh
```
<img src="images/07-config-ACL-management-r1.png " alt=" 07-config-ACL-management-r1" width="50%"> <img src="images/08-config-ACL-en r1-pc it.png " alt=" 08-config-ACL-en r1-pc it" width="50%"> 


A diferencia de una ACL aplicada sobre una interfaz mediante `ip access-group`, `access-class` controla específicamente el acceso hacia las líneas administrativas del propio dispositivo.

La sentencia:

```cisco
20 deny any log
```

permite que además se registren los intentos de acceso provenientes de direcciones no autorizadas, preparando estos eventos para su futura centralización mediante Syslog.

<img src="images/09-deny-any-log.png " alt=" 09-deny-any-log" width="70%">
---

## Administración mediante VLAN 99

Los switches del laboratorio utilizan la VLAN 99 como red destinada a Management.

Por ejemplo:

```text
SW1-CORE
VLAN 99
192.168.99.2
```

La estación `IT-Admin`, ubicada en VLAN 20, puede alcanzar las interfaces de administración mediante el enrutamiento Inter-VLAN proporcionado por `R1-EDGE`.

```text
IT-Admin
192.168.20.155
       │
       ▼
R1-EDGE
       │
       ▼
VLAN 99 - Management
       │
       ▼
SW1-CORE
192.168.99.2
```

Los switches fueron configurados con su gateway de administración:

```cisco
no ip routing
ip default-gateway 192.168.99.1
```

Esto permite que los switches operando en Capa 2 respondan correctamente a conexiones provenientes de otras redes.

La misma política de SSH y la ACL `SSH-MANAGEMENT` fueron aplicadas sobre los switches administrables del laboratorio.

---

## Validación del acceso autorizado

Desde `IT-Admin` se realizó una conexión SSH hacia `R1-EDGE`.

La autenticación fue completada correctamente mediante el usuario local configurado en el router.

La sesión permitió acceder al dispositivo con privilegios administrativos.

<img src="images/06-conexion-ssh-it -r1.png " alt=" 06-conexion-ssh-it -r1" width="70%">

También se verificó el acceso hacia los switches mediante sus direcciones pertenecientes a la VLAN 99.

---

## Validación de acceso no autorizado

Para comprobar el funcionamiento de `SSH-MANAGEMENT`, se realizó un segundo intento desde un host perteneciente a la VLAN 10 - Administración.

conexion ssh (10)
<img src="images/10-conexion-ssh- admin-r1.png " alt=" 10-conexion-ssh- admin-r1" width="60%">

El host conservaba conectividad IP con el router:

```text
Host VLAN 10 → Ping R1-EDGE ✅
```
pero no logró establecer una sesión SSH:

```text
Host VLAN 10 → SSH R1-EDGE ❌
```

Esto demuestra que la conectividad entre redes continúa funcionando, mientras que el acceso al plano de administración permanece restringido.

gif 11
<img src="images/11-match-en-acl.gif " alt=" 11-match-en-acl" width="60%">

Posteriormente, el comando:

```cisco
show ip access-lists SSH-MANAGEMENT
```

mostró coincidencias sobre la regla:

```cisco
20 deny any log
```
confirmando que el intento había sido rechazado por la política configurada.

---

## Compatibilidad con IOSv

Durante las pruebas, el cliente OpenSSH utilizado en `IT-Admin` rechazó inicialmente algunos algoritmos ofrecidos por la imagen IOSv debido a que se encuentran deshabilitados por defecto en versiones modernas de OpenSSH.

Para establecer la conexión dentro del entorno de laboratorio fue necesario habilitar explícitamente compatibilidad con los algoritmos ofrecidos por IOSv:

```bash
ssh \
-oKexAlgorithms=+diffie-hellman-group14-sha1 \
-oHostKeyAlgorithms=+ssh-rsa \
Katmin@192.168.20.1
```

Esta configuración se utilizó únicamente para mantener compatibilidad con la imagen de IOS empleada en GNS3 y no representa una recomendación para entornos productivos actuales.

---

## Resultado

La implementación permitió establecer un esquema de administración remota controlado para la infraestructura del laboratorio.

✅ SSHv2 habilitado para la administración remota.
✅ Claves RSA de 2048 bits configuradas.
✅ Autenticación mediante usuario local protegido con `secret`.
✅ Telnet deshabilitado en las líneas VTY.
✅ Estación `IT-Admin` dedicada para tareas administrativas.
✅ Acceso SSH restringido exclusivamente a `192.168.20.155`.
✅ Intentos provenientes de hosts no autorizados bloqueados mediante ACL.
✅ Logging habilitado para intentos de acceso no autorizados.

## ⏩Próximos pasos 

En la siguiente etapa se implementará Hardening de dispositivos. 
[Ir a la siguiente sección](11-hardening.md)
