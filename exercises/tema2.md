### Ejercicio 1

#### Calcular la disponibilidad del sistema si tenemos dos réplicas de cada elemento (en total 3 elementos en cada subsistema):

| Availability % | 1 Component | 2 Components | 3 Components   |
| -------------- | ----------- | ------------ | -------------- |
| Web            |    85,00%   |  97,750000%  |  99.66250000%  |
| Application    |    90,00%   |  99.000000%  |  99.90000000%  |
| Database       |    99.90%   |  99.999900%  |  99.99999999%  |
| DNS            |    98.00%   |  99.960000%  |  99.99920000%  |
| Firewall       |    85.00%   |  97.750000%  |  99.66250000%  |
| Switch         |    99.00%   |  99.900000%  |  99.99990000%  |
| Data Center    |    99.99%   |  99.999999%  | 100.00000000%  |
| ISP            |    95.00%   |  99.750000%  |  99.98750000%  |
| System Avaib.  |    59.87%   |  94.301929%  |  99.21351551%  |

General Case:

As = Acn-1 + ((1 - Acn-1) * Acn)

Example Web:

Web1 	= 85%
Web2 	= 85% + (1-85%) * 85% = 97.75%
Web3 	= 97.75% + (1-97.75%) * 85% = 99.6625%

- - -

### Ejercicio 2

#### Buscar frameworks y librerías para diferentes lenguajes que permitan hacer aplicaciones altamente disponibles con relativa facilidad. Como ejemplo, examina PM2 <https://github.com/Unitech/pm2>, que sirve para administrar clústeres de NodeJS.

**Vanilla**

Es como se le conoce a escribir JavaScript puro, como prefieren trabajar la mayoría de desarrolladores.

**React**

Librería de Facebook enfocada en la creación de vistas, con particularidades como: *patrones de eventos*, que permiten actualizar las mencionadas vistas cuando los datos hayan sido modificados, aportando una carga en tiempo real a nuestro desarrollo.

**VueJS**

Relacionado con AngularJS, añadiendo sus partes favoritas de este framework, para crear algo muy ligero. Desde 2014 está disponible a toda la comunidad y es un proyecto Open Source.

**Angular**

Es un framework para aplicaciones web desarrollado en TypeScript, de código abierto y mantenido por Google, que se utiliza para crear y mantener aplicaciones web de una sola página.

**Laravel**

Integra una buena gestión de rutas, sistema de autenticación, soporte para migraciones de otros entornos y el sistema de gestión de modelos Blade, entre otras cosas. Sus sistemas de templates o plantillas, llamado Blade, es muy ligero y tiene una sintaxis bastante elegante, facilitando la mayor parte de las tareas como as acciones de login, el control de sesiones, la gestión de las páginas en caché y demás.

**Symfony**

Es uno de los frameworks de PHP más flexibles y escalables que existen para el desarrollo del aplicaciones que utilizan el modelo vista-controlador o MVC. Existen multitud de componentes para PHP que pueden ser utilizados como Form Config para la gestión de formularios, Translation para la gestión de traducciones, Templating para la gestión de los temas y Security, que tal y como su nombre indica es un componente de seguridad.

**Phalcon**

La velocidad de proceso es una prioridad. Cuando su velocidad es comparada con la de otros frameworks, gana de lejos por su mínimo uso de memoria de acuerdo a los tests que se han realizado. A diferencia de otros frameworks, Phalcon está escrito con C/C++ para optimizar su velocidad y tener el mejor rendimiento posible.

**Zend Framework**

 Propio de PHP, escalable y orientado a objetos que disponen de muchas funcionalidades. Ha sido creado por los también creadores del lenguaje PHP y en su última versión se incluyen muchas mejoras con respecto a versiones anteriores. Es un framework muy robusto, pero no se recomienda para pequeños proyectos, ya que conllevará un innecesario tiempo de desarrollo adicional.

**Flight**

Se trata de un pequeño framework de PHP al que es muy sencillo agregar nuevas funcionalidades. Su documentación es tan básica como fácil de aprender, por lo que resulta un framework sencillo incluso para principiantes.

**Fat-Free**

Fat-Free es un pequeño framework para PHP que a su vez es potente, fácil de utilizar y ligero (solo ocupa 100KB). Está desarrollado utilizando PHP y dispone de sistema de gestión de rutas, soporte nativo para aplicaciones multilenguaje y sistema de caché.

**Express**

Express es una infraestructura de aplicaciones web Node.js mínima y flexible que proporciona un conjunto sólido de características para las aplicaciones web y móviles. 

**Koa**

Es la nueva generación de framework para la web. Está creada por desarrolladores que estuvieron creando Express y entre sus cualidades, con las que pretenden que este framework sea mejor que Express, se encuentran: tamaño, mayor expresividad en los métodos y mayor solidez a la hora de crear aplicaciones web y API’s.

**Hapi**

HapiJS es otro framework de NodeJS para construir aplicaciones web, que se entra en que el código que crees (para tu aplicación) sea reusable en vez de estar perdiendo el tiempo con código innecesario.

- - -

### Ejercicio 3

#### ¿Cómo analizar el nivel de carga de cada uno de los subsistemas en el servidor?

**Nagios**

Se trata de un sistema de monitorización de redes y servidores, que se encarga de vigilar los equipos (hardware) y los servicios (software) que se especifiquen, alertando cuando el comportamiento no es el deseado. Puedo monitorizar la mayoría de servicios, ya sea de correo (SMTP, POP3, IMAP), web (APACHE, NGINX, TOMCAT), los recursos de sistema de hardware (uso de disco, memoria, puertos, procesador, etcétera). Además puede realizar todas estas tareas independientemente del sistema operativo o la mayoría de hardware (servidores físicos, plataformas de virtualización, switches, routers y una larga lista).

**Zabbix**

Diseñado para controlar los estados de varios servicios de red, servidores y maquinaria (hardware). Entre sus principales características encontramos chequeos simples de sistemas para verificar su disponibilidad y su nivel de respuesta. Permite la instalación de agentes en la mayoría de sistemas. Además, nos ofrece una alternativa a la instalación de agentes, utilizando desde los protocolos SMTP, TCP o ICMP, hasta otros como el clásico Telnet. Incluye mecanismos de notificación en tiempo real como, por ejemplo, XMPP (Jabber).

**PandoraFMS**

Se trata de un sistema pensado para gestionar infraestructuras de IT. Esto incluye equipos de red y servidores, tanto de la familia UNIX como Microsoft Windows, infraestructura virtualizada y todo tipo de aplicaciones, incluyendo algunas populares, como SAP. Trabaja muy bien con los dispositivos de red, utilizando el lenguaje propio de estos elementos (SNMP). También puede procesar las estadísticas Netflow ofrecidas por los routers.

**Monit**

Se trata de una herramienta para la monitorización de procesos en sistemas UNIX y UNIX-like, como GNU/Linux o *BSD. Con él podemos ver el estado de un sistema ofrecido directamente desde el intérprete de comandos, o vía un frontend web.  Si utilizas Ruby on Rails y el servidor web Mongrel es bastante probable que ya hayas trabajado con él. Muchos productos populares como Twitter lo emplean.

- - -

### Ejercicio 4

#### Buscar ejemplos de balanceadores software y hardware:

	* Software

		- HAProxy
		- Nginx
		- LVS
		- Ultra Monkey
		- Pound
		- Pfsense
		- Apache
		- Pirhana	
		- NLB (Microsoft)

	* Hardware

		- Local Director (Cisco)	
		- BIG-IP (F5)
		- Radware
		- Barracuda
		- Resonate
		- Coyote Point / Fortinet FortiADC
		- Citrix
		- Kemp Technologies
		- Zen Load Balancer
		- NGINX Plus
		- Amazon ELB