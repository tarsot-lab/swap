### Ejercicio 1

#### Aplicar, con iptables, una política de: denegar/permitir todo el tráfico, comprobando el funcionamiento en una de las máquinas de prácticas.

Como ya se implementó en la sesión de prácticas, primero hemos de eliminar todas las reglas existentes (recordemos que ya existe un script que se ejecuta al inicio del sistema) para la máquina que asume la función de firewall:

	$ iptables -F
	$ iptables -X
	$ iptables -Z
	$ iptables -t nat -F

Posteriormente, aplicamos una regla de denegación de todo el tráfico:

	$ iptables -P INPUT DROP
	$ iptables -P OUTPUT DROP
 	$ iptables -P FORWARD DROP

De ese modo, prácticamente bloqueamos la VM, por lo que hemos de permitir ciertas reglas en el tráfico. Para permitirl cualquier tipo de acceso, ejecutamos:

	$ iptables -P INPUT ACCEPT
	$ iptables -P OUTPUT ACCEPT
	$ iptables -P FORWARD ACCEPT

Si bien, la recomendación es permitir solo aquellos servicios que vayamos a utilizar.

Por otro lado, conviene recordar el uso de UFW (Uncomplicated Firewall), herramienta que nos facilita la configuración del firewall basado en Iptables. Existe una versión aún más cómoda para aplicar estas reglas, llamada GUFW (GUI). 

- - -

### Ejercicio 2

#### Comprobar qué puertos tienen abiertos nuestras máquinas, su estado, y qué programa o demonio lo ocupa.

	$ netstat -tulnp

	Active Internet connections (only servers)
	Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
	tcp        0      0 0.0.0.0:45867           0.0.0.0:*               LISTEN      -               
	tcp        0      0 0.0.0.0:34924           0.0.0.0:*               LISTEN      1024/rpc.mountd 
	tcp        0      0 0.0.0.0:48622           0.0.0.0:*               LISTEN      1024/rpc.mountd 
	tcp        0      0 0.0.0.0:40078           0.0.0.0:*               LISTEN      1024/rpc.mountd 
	tcp        0      0 0.0.0.0:111             0.0.0.0:*               LISTEN      795/rpcbind     
	tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      1112/apache2    
	tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      1309/sshd       
	tcp        0      0 0.0.0.0:56540           0.0.0.0:*               LISTEN      843/rpc.statd   
	tcp        0      0 0.0.0.0:2049            0.0.0.0:*               LISTEN      -               
	tcp6       0      0 :::111                  :::*                    LISTEN      795/rpcbind     
	tcp6       0      0 :::48336                :::*                    LISTEN      -               
	tcp6       0      0 :::33426                :::*                    LISTEN      1024/rpc.mountd 
	tcp6       0      0 :::22                   :::*                    LISTEN      1309/sshd       
	tcp6       0      0 :::35638                :::*                    LISTEN      843/rpc.statd   
	tcp6       0      0 :::43194                :::*                    LISTEN      1024/rpc.mountd 
	tcp6       0      0 :::36927                :::*                    LISTEN      1024/rpc.mountd 

Una forma más confiable de verificar qué puertos se están escuchando, es a través de un escáner como *nmap*:

	$ sudo nmap -sT -O 192.168.56.14

	Starting Nmap 7.40 ( https://nmap.org ) at 2019-05-12 13:17 CEST
	Nmap scan report for 192.168.56.14
	Host is up (0.00023s latency).
	Not shown: 996 closed ports
	PORT     STATE SERVICE
	22/tcp   open  ssh
	80/tcp   open  http
	111/tcp  open  rpcbind
	2049/tcp open  nfs
	MAC Address: 08:00:27:CC:35:9E (Oracle VirtualBox virtual NIC)
	Device type: general purpose
	Running: Linux 2.6.X|3.X
	OS CPE: cpe:/o:linux:linux_kernel:2.6 cpe:/o:linux:linux_kernel:3
	OS details: Linux 2.6.32 - 3.5
	Network Distance: 1 hop

	OS detection performed. Please report any incorrect results at https://nmap.org/submit/ .
	Nmap done: 1 IP address (1 host up) scanned in 15.33 seconds

- - -

### Ejercicio 3

#### Buscar información acerca de los tipos de ataques más comunes en servidores web (p.ej. secuestros de sesión). Detallar en qué consisten, y cómo se pueden evitar.

Algunos de los que se citan, a continuación, son empleados por hackers que intentan explorar las vulnerabilidades de las máquinas atacadas:

* Denegación de servicio (DoS) o Denegación de Servicio Distribuida (DDoS): son las formas más comunes para 'congelar' el funcionamiento de un sitio web. Estos son los intentos de inundar un sitio con solicitudes externas, quedando no disponible para los usuarios reales. Estos ataques, por lo general, se dirigen a puertos específicos, rangos de IP o redes completas, pero se pueden dirigir a cualquier dispositivo o servicio conectado.

	* Tipos: ICMP Flood Attack, Tear Drop Attack, Smurf Attack, SYN Flood, Land Attack, Jolt Dos Attack, Fraggle Dos Attack.

	* Cómo prevenir este tipo de ataques:

		- Aplicación de filtrado de enrutador.
		- Bloquear direcciones IP sin usar.
		- Permitir el acceso a la red solo al tráfico deseado.
		- Deshabilitar servicios de red innecesarios.
		- Actualización de antivirus regularmente.
		- Tener una muy buena política de contraseñas.
		- Limitar la cantidad de ancho de banda de la red.
		- Uso de la red de filtrado de acceso.

* Escaneo de puertos: que ayuda al atacante a encontrar qué puertos están disponibles para ejecutar un posterior ataque.

	* Prevención: cerrar los servicios innecesarios en los sistemas de destino. Es decir, si está ejecutando un servidor web (http), este debe ser el único servicio ofrecido a través de ese puerto 80.

* ARP Spoofing - ARP Poison Routing (APR), es una técnica utilizada para atacar una red cableada o inalámbrica de Ethernet. ARP Spoofing puede permitir que un atacante detecte frameworks de datos en una red de área local (LAN), modifique el tráfico o detenga el tráfico por completo. El ataque solo se puede usar en redes que realmente usan ARP y no en otro método de resolución de direcciones.

* ACK flood - Esta es una técnica para enviar un paquete TCP / ACK al objetivo a menudo con una dirección IP falsificada. Es muy similar a los ataques de inundación TCP / SYN.

* FTP Bounce - El atacante puede conectarse a los servidores FTP y tiener la intención de enviar archivos a otros usuarios / máquinas que usan el comando PORT. Para que el servidor FTP intente enviar el archivo a otras máquinas en un puerto específico y verifique que el puerto esté abierto.

* TCP Session Hijacking - El "Hacker" toma la sesión TCP existente, ya establecido entre las dos partes. En la mayoría de las sesión TCP la autenticación ocurre al comienzo de la sesión, los piratas informáticos realizan este ataque en dicho momento.

* Man-In-The-Middle - Un ataque MITM ocurre cuando una comunicación entre dos sistemas es interceptada por una entidad externa. Esto puede suceder en cualquier forma de comunicación en línea, como correo electrónico, redes sociales, navegación web, etc. No solo están tratando de escuchar nuestras conversaciones privadas, sino que también pueden dirigir toda la información dentro de los dispositivos.

	* Prevención: mediante certificados de autenticación y S/MIME (Extensiones de correo de Internet seguras / multipropósito).

* Ingeniería social: técnica que manipula a las personas para que renuncien a la información confidencial.

	* ¿Cómo prevenir estos ataques de tanta proliferación?:

		* Eliminar cualquier solicitud de información financiera o contraseñas.
		* Rechazar las solicitudes de ayuda u ofertas de ayuda.
		* Controlar qué descargamos.
		* Desconfiar de ofertas extranjeras.
		* Asegurar los dispositivos informáticos.

* OS Finger Printing: es un método que se utiliza para determinar qué sistema operativo se ejecuta en una máquina remota. Al identificar el sistema operativo exacto de un host, un atacante puede lanzar un ataque preciso contra una máquina destino.

* ICMP Tunneling: utilizado para eludir los firewalls que no bloquean los paquetes ICMP, o para establecer un canal de comunicación cifrado y difícil de rastrear entre dos computadoras sin interacción directa de la red. Un túnel ICMP establece una conexión encubierta entre dos computadoras remotas (un cliente y un proxy), utilizando solicitudes de eco ICMP y paquetes de respuesta.

	* La solución para evitar el túnel ICMP pasa por:

		- Permitir a los administradores usar mensajes ICMP libremente.
		- Permitir el paso del gran tamaño de ICMP.
		- Debería funcionar incluso en el caso de una fuerte autenticación y carga útil encriptada.
		- Debería funcionar incluso cuando la máquina está detrás de cortafuegos personales a nivel de aplicación.

* Ataque de secuencia TCP: es un intento de predecir el número de secuencia utilizado para identificar los paquetes en una conexión TCP, que se puede usar para duplicar paquetes que conducen al secuestro de la sesión.

	* Prevención: bloquear los paquetes enrutados de origen y paquetes de datos con direcciones dentro de las propias redes. Los servicios, que dependen de la autenticación basada en IP, deberían desconectar una conexión por completo al detectar que están presentes las opciones enrutadas de origen.

* CAM Table Overflow: una máquina contiene información de red en esta tabla, así como las direcciones MAC disponibles en los puertos físicos del conmutador y los parámetros de VLAN asociados. Los desbordamientos de la tabla CAM, ocurren cuando una entrada de direcciones MAC se inunda en la tabla y por lo que se alcanza el umbral de la tabla CAM. Esto hace que el conmutador actúe como un concentrador, inundando la red con tráfico fuera de todos los puertos. La inundación causada por un desbordamiento de tabla CAM está limitada a la VLAN de origen, por lo tanto, no afecta a otras VLAN en la red.

	* Prevención: para prevenir este tipo de ataque, cambiaremos a un puerto de acceso, emitiendo acceso en modo de puerto de conmutación. Luego asignaremos la cantidad máxima de direcciones MAC en las que se almacenará la tabla CAM para esta interfaz.

* Ataques a aplicaciones Web:

	* Inyección o inserción SQL: que ayuda al hacker a ejecutar un código debido a la presencia de vulnerabilidad en la capa de la base de datos de la Aplicación. En consecuencia, el código obtendrá datos confidenciales o incluso comprometerá la aplicación en sí.

	* Cross-Site Request: conocido como ataque con un solo clic o sesión y abreviado como CSRF ("sea-surf") o XSRF, es un tipo de exploit malicioso de un sitio web mediante el cual se transmiten comandos no autorizados de un usuario en el que el sitio web confía. 

	* Ataque de envenenamiento de cookies: que implican la modificación de los contenidos de una cookie (información personal almacenada en la computadora de un usuario web) para eludir los mecanismos de seguridad. Al usar ataques de envenenamiento de cookies, los atacantes pueden obtener información no autorizada sobre otro usuario y robar su identidad.

	* Ataques de phishing: es el proceso criminalmente fraudulento de intentar adquirir información sensible como nombres de usuario, contraseñas y detalles de tarjetas de crédito haciéndose pasar por una entidad confiable en una comunicación electrónica.

	* Web Defacement: es un ataque a un sitio web que cambia la apariencia visual del sitio. Estos son típicamente el trabajo de los crackers del sistema, que entran en un servidor web y reemplazan el sitio web alojado con uno propio. Lo más probable es que este tipo de ataques se hagan intencionalmente para arruinar la reputación de la compañía que ha alojado este sitio web.

	* Buffer Overflow: o el desbordamiento del búfer, es una anomalía en la que un proceso almacena datos en un búfer fuera de la memoria que el programador reservó para ello. Los datos adicionales sobrescriben la memoria adyacente, que puede contener otros datos, incluidas variables de programa y datos de control de flujo del programa. Esto puede provocar errores de acceso a la memoria, resultados incorrectos, finalización del programa o una violación de la seguridad del sistema. Esta vulnerabilidad es completamente un error del Programador.

	* Navegación forzada: es un ataque cuyo objetivo es enumerar y acceder a los recursos a los que la aplicación no hace referencia, pero que aún son accesibles. Por ejemplo, directorios como config, backup, logs a los que se puede acceder pueden revelar mucha información sobre la aplicación en sí, contraseña, actividades, etc.

	* División de respuesta HTTP: un atacante pasa datos maliciosos a una aplicación vulnerable, y la aplicación incluye los datos en un encabezado de respuesta HTTP. Este ataque en sí no causa ningún daño, pero daría lugar a otros ataques sensibles como XSS.
	
	* Defectos de inyección: que permiten a los atacantes retransmitir código malicioso a través de una aplicación web a otro sistema. Estos ataques incluyen llamadas al sistema operativo a través de llamadas al sistema, el uso de programas externos a través de comandos del shell, así como llamadas a bases de datos de backend a través de SQL (es decir, inyección de SQL). Los scripts completos escritos en Perl, Python y otros lenguajes pueden ser inyectados en aplicaciones web mal diseñadas y ejecutado. Cada vez que una aplicación web utiliza un intérprete de cualquier tipo, existe el peligro de un ataque de inyección. Cada vez que una aplicación web utiliza un intérprete de cualquier tipo, existe el peligro de un ataque de inyección.

		* Prevención: 

			- Las revisiones de código, los programas de recompensa de errores y los escáneres de código, que deberían implementarse durante todo el ciclo de vida de la aplicación.
			- Usar procedimientos almacenados con parámetros que se parametrizan automáticamente.
			- Implementando CAPTCHA o incitando a los usuarios a responder preguntas. Esto asegura que un formulario y una solicitud sean enviados por un humano y no por un bot.
			- Utilizar un cortafuegos de aplicación web (WAF) para supervisar la red y bloquear posibles ataques.

* Malware: que incluye virus informáticos, gusanos, caballos de Troya, spyware, adware deshonesto, software delictivo, la mayoría de los rootkits y otro software malicioso y no deseado.

	* Prevención: 

		- Instalar software Anti-Virus / Malware.
		- Mantener el software antivirus actualizado.
		- Ejecutar escaneos regulares programados del antivirus.
		- Mantener el sistema operativo al día.
		- Asegurar la red.
		- Mantener la información personal segura.
		- No usar Wi-Fi abierto.
		- Haz una copia de seguridad de los archivos.
		- Contaseñas más seguras.


Es conveniente estar actualizado sobre seguridad, documentándose a través de foros especializados, como <a href="https://underc0de.org/foro/seguridad-en-servidores/" target="_blank">undercode.</a>