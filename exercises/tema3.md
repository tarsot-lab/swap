### Ejercicio 1

#### Buscar con qué órdenes de terminal o herramientas podemos configurar, bajo Windows y bajo Linux, el enrutamiento del tráfico de un servidor para pasar el tráfico desde una subred a otra.

* LINUX

	* Activando IP forwarding

	Si necesitamos usar nuestra máquina GNU/Linux como un ruteador o puerta de acceso a Internet compartida, ejecutamos esta línea en una terminal (como root):

	echo 1 > /proc/sys/net/ipv4/ip_forward

	* Uso de sysctl

	Esta herramienta, nos permite cambiar los valores del kernel en tiempo real y con ello habilitar el IP forward o no.


* WINDOWS

	Windows Server puede funcionar como enrutador, para que nuestra red privada tenga acceso a La Red. Para ello, usaremos dos tarjetas de red, cada una de ellas con su configuración IP. Una de las tarjetas estará conectada en el segmento de la red privada y la otra quedará conectada al segmento de red en el que esté el Router.

	NetShell **nesh**

	Este intérprete nos permite cambiar rápidamente de una puerta de enlace a otra. Por ejemplo:

	netsh interface ip set address name=”LAN”  gateway=”192.168.1.1″ gwmetric=30  
	netsh interface ip set address name=”WLAN” gateway=”192.168.2.1″ gwmetric=20

	Se trata de bajar la métrica (forma en que una tabla de enrutamiento prefiere una interfaz en vez de otra) de la GW inalámbrica, asignándola como preferida.

- - -

### Ejercicio 2

#### Buscar con qué órdenes de terminal o herramientas gráficas podemos configurar, bajo Windows y bajo Linux, el filtrado y bloqueo de paquetes.

* LINUX

	* Uso de iptables e ipchains (consola)

	Son dos  métodos de filtrado de paquetes, dentro del kernel de Linux, que usan cadenas de reglas y deciden qué hacer con los paquetes que cumplen determinadas reglas. 

	* Kmyfirewall.

	* Shorewall.

	* Guarddog.

	* FireStarter.

	* IPCop.


* WINDOWS	

	* Usamos *netsh advfirewall firewall* para controlar el comportamiento de Firewall.

	* Privatefirewall.

	* BlackICE.

	* ATGuard.

	* ZoneAlarm.

	* Agnitum Outpost Firewall.

	* WinRoute Pro.

