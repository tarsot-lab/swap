#### Buscar información sobre las tareas o servicios web para los que se usan más los programas que siguen a continuación:

* **Apache:** Apache HTTP Server, es un software de servidor web gratuito y de código abierto con el cual se ejecutan el 46% de los sitios web de todo el mundo. Se usa para enviar páginas web estáticas y dinámicas. Se integra junto a MySQL y los lenguajes de programación PHP, Perl, Python y Ruby.

	- Pros:

   		- De código abierto y gratuito, incluso para uso comercial.
   		- Software confiable y estable.
   		- Parches de seguridad regulares y actualizados con frecuencia.
   		- Flexible debido a su estructura basada en módulos.
   		- Fácil de configurar para principiantes.
   		- Multiplataforma (funciona tanto en servidores Unix como en Windows).
   		- Viene listo para trabajar con sitios de WordPress.
    	- Enorme comunidad y soporte fácilmente disponible en caso de cualquier problema.

	- Contras:

   		- Problemas de rendimiento en sitios web con demasiado tráfico.
   		- Demasiadas opciones de configuración pueden generar vulnerabilidades de seguridad.

- - -

* **Nginx:** es otra aplicación de servidor web, creada para resolver el denominado problema c10k, lo que significa que un servidor web, que utiliza subprocesos para manejar las solicitudes de los usuarios, no puede administrar más de 10,000 conexiones al mismo tiempo.

	Nginx tiene una arquitectura guiada por los eventos, que no crea un nuevo proceso para cada solicitud. En cambio, maneja todas las solicitudes entrantes en un solo proceso. Este proceso maestro, gestiona varios procesos de trabajo que realizan el procesamiento real de las solicitudes. Por tanto, distribuye las solicitudes de los usuarios entre los procesos de trabajo de una manera eficiente, lo que conduce a una escalabilidad mucho mejor.

	Nginx como tal, también podría actuar como IMAP Proxy, un proxy para el protocolo IMAP (Internet Message Access Protocol) de correo. Como proxy inverso nos va a permitir:

    - Añadir seguridad, protegiendo al resto de servidores web del ataque directo de los usuarios.
    - Reescribir las URLs según nuestras necesidades.
    - Securizar el acceso a nuestras aplicaciones web con HTTPS, es decir, podremos enrutar la petición HTTP hacia HTTPS y securizar la comunicación entre los dos puntos.

- - -

* **thttpd:** es un servidor web de código libre, disponible para la mayoría de las variantes de Unix. Se caracteriza por ser simple, pequeño, portátil, rápido, y seguro, ya que utiliza los requerimientos mínimos de un servidor HTTP. Está escrito en lenguaje de programación C.

	- Entre sus características destacan: soporte para HTTP, HTTPS/TLS/SSL, proxy HTTP, SCGI e integración con FASTCGI para despacho de páginas dinámicas (como PHP). También destaca por ofrecer compresión HTTP, autenticación LDAP y permitir el uso de virtual hosts.

- - -

* **Cherokee:** Si bien no llega a la rapidez que tiene Nginx, en cuanto a velocidad de procesamiento y despacho de datos, es muy superior a Apache.

	- Algunas de sus principales características:

    	- Administración gráfica basada en la web: que te permite configurar el servidor web de manera fácil, tanto para los parámetros generales del mismo, como para instalar cada virtual host y las optimizaciones a nivel de rendimiento (tweaks).
    	
    	- Configuración fácil de PHP: como pocos servidores, Cherokee permite configurar PHP de forma fácil con un par de clicks desde la interfaz web, sin tocar la consola o el archivo .php.ini.
    
    	- Actualizaciones dinámicas: es decir, no necesita reiniciarse para aplicar cambios en su core tras aplicar cualquier actualización en el paquete principal del servicio.
    
    	- Balanceo de carga: permite usarse como balanceador de carga para trabajar frente a servidores como Apache y Nginx en el backend.
    	
    	- Altísima velocidad de despacho: tanto que algunos lo comparan con Lighttpd y Nginx, sobre todo en lo que despacho de ficheros estáticos se refiere.

- - -

* **Node.js:** es un entorno de ejecución, para JavaScript, construido con el motor de JavaScript V8 de Chrome, que está diseñado para generar aplicaciones web de forma altamente optimizada. 

	Es de código abierto, multi-plataforma, que permite a los desarrolladores crear toda clase de herramientas de lado servidor y aplicaciones en JavaScript. La ejecución en tiempo real está pensada para ejecutarse directamente en una computadora o sistema operativo de servidor. Como tal, el entorno omite las APIs de JavaScript específicas del explorador web y añade soporte para APIs de sistema operativo más tradicionales que incluyen HTTP y bibliotecas de sistemas de ficheros.

	- Alguna de sus ventajas:

    	- Gran rendimiento: Node ha sido diseñado para optimizar el rendimiento y la escalabilidad en aplicaciones web y es un muy buen complemento para muchos problemas comunes de desarrollo web (ej, aplicaciones web en tiempo real).
    
    	- El código está escrito en "simple JavaScript", lo que significa que se pierde menos tiempo ocupándose de las "conmutaciones de contexto" entre lenguajes cuando estás escribiendo tanto el código del explorador web como del servidor.
    
    	- El gestor de paquetes de Node (NPM del inglés: Node Packet Manager) proporciona acceso a cientos o miles de paquetes reutilizables. Tiene además la mejor en su clase resolución de dependencias y puede usarse para automatizar la mayor parte de la cadena de herramientas de compilación.
    
    	- Es portable, con versiones que funcionan en Microsoft Windows, OS X, Linux, Solaris, FreeBSD, OpenBSD, WebOS, y NonStop OS. 

    	- Además, está bien soportado por muchos de los proveedores de hospedaje web, que proporcionan infraestructura específica y documentación para hospedaje de sitios Node.
