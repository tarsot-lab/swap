### Práctica 3. Balanceo de carga 

### Nginx

Desde el esquema anterior, incluimos un nuevo nodo que hará de balanceador (LB):


|   **Node0**   |   **Node1**   |   **Node2**   |
| :-----------: | :-----------: | :-----------: |
| Web Server 1  | Web Server 2  | Load Balancer |
| 192.168.56.10 | 192.168.56.11 | 192.168.56.12 |

En esta fase, se prueban varias configuraciones sobre el nuevo nodo. Por tanto y de inicio, nos aseguramos de tener libre el puerto 80 (HTTP), para que el software de balanceo pueda recibir peticiones desde fuera.

    vagrant@node2:~$ sudo apt-get install nginx

Comprobamos que está funcionado como servidor web:

<img src="./images/nginx_welcome.png" alt="Welcome to nginx" width="550" height="250"/>

La idea es configurarlo como LB, haciendo uso de la configuración necesaria para que funcione como tal:

    vagrant@node2:$ cat /etc/nginx/conf.d/default.conf 

    upstream apaches {

        server 192.168.56.10;
        server 192.168.56.11;

    }

    server{
        listen 80;
        server_name balanceador;

        access_log /var/log/nginx/balanceador.access.log;
        error_log /var/log/nginx/balanceador.error.log;
        root /var/www/;
      
        location /
        {

        proxy_pass http://apaches;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_http_version 1.1;
        proxy_set_header Connection "";
        }
    }

Una vez comprobado que el código es correcto, reiniciamos el servicio:

    vagrant@node2:$ sudo service nginx restart

Seguidamente, desde otro nodo, comprobamos el funcionamiento mediante el algoritmo Round Robin:

    vagrant@node0:~$ curl node2
    <HTML>
    <BODY>
    <H1>1st Web Server. It works! :-)</H1>
    </BODY>
    </HTML>
    vagrant@node0:~$ curl node2
    <HTML>
    <BODY>
    <H1>2nd Web Server. It works! :-)</H1>
    </BODY>
    </HTML>

Cabe destacar que podemos establecer un algoritmo adicional, si modificamos el parámetro *upstream backend* de este modo:

    upstream backend {
        server node0 weight=3;
        server node1 max_fails=3 fail_timeout=30s;
        server node3;
    }

En este ejemplo, las peticiones se distribuyen mediante el algortimo Round Robin, aunque se ha modificado. De este modo, cada *cinco peticiones*, tres van al primer nodo y otra a cada unas de las restantes máquinas del grupo. No obstante, si ocurre un error, la petición se pasará al siguiente servidor. Además, en el segundo servidor, se establecen hasta tres intentos fallidos de conexión antes considerar ese servidor como no operativo, y esperará treinta segundos entre fallos.

### HAProxy

Para hacer uso de este nuevo balanceador, paramos el servicio anterior, puesto que debemos dejar libre el mismo puerto 80:

    vagrant@node2:$ sudo service nginx stop

Ahora pasamos a instalar HAProxy:

    vagrant@node2:$ sudo apt-get install haproxy

Y lo configuramos para que realice la función de LB:

    vagrant@node2:~$ sudo nano /etc/haproxy/haproxy.cfg 

    global
            daemon
            maxconn 256

    defaults
            mode http
            timeout connect 4000
            timeout client 42000
            timeout server 43000

    frontend http-in 
            bind *:80
            default_backend servers

    backend servers
            server m1 192.168.56.10:80 maxconn 32
            server m2 192.168.56.11:80 maxconn 32

Lo que significa que, de modo análogo, nuestro nuevo LB espera conexiones entrantes HTTP (80), que redirige a los dos nodos Web Server que escuchan a través de ese mismo puerto.

Lanzamos el servicio HAProxy, mediante el comando: 

    vagrant@node2:$ sudo /usr/sbin/haproxy -f /etc/haproxy/haproxy.cfg

Igual que con Nginx, hacemos la prueba de funcionamiento mediante:

    vagrant@node0:~$ curl node2
    <HTML>
    <BODY>
    <H1>1st Web Server. It works! :-)</H1>
    </BODY>
    </HTML>
    vagrant@node0:~$ curl node2
    <HTML>
    <BODY>
    <H1>2nd Web Server. It works! :-)</H1>
    </BODY>
    </HTML>

Comprobamos, efectivamente, que funciona.

### Pound

Volvemos a deshabilitar el servicio que tenemos corriendo. En este caso, es HAProxy, para que el nuevo comience a ejercer las funciones de LB:

    sudo killall haproxy &

Instalamos el nuevo software LB:

    sudo apt install pound

Y lo configuramos:

    vagrant@node2:$ sudo nano /etc/pound/pound.cfg

    ListenHTTP
        Address 192.168.56.12
        Port    80

        HeadRemove "X-Forwarded-For"

        Service
                BackEnd
                        Address 192.168.56.10
                        Port    80
                End
                BackEnd
                        Address 192.168.56.11
                        Port    80
                End
            End
    End

Debemos habilitar el funcionamiento:

    vagrant@node2:$ sudo nano /etc/default/pound

Y cambiar la línea startup=0 por startup=1.

Lanzamos el demonio:

    vagrant@node2:$ sudo /etc/init.d/pound start

Volvemos a realizar las mismas pruebas anteriores:

vagrant@node0:~$ curl node2

    <HTML>
    <BODY>
    <H1>1st Web Server. It works! :-)</H1>
    </BODY>
    </HTML>
    vagrant@node0:~$ curl node2
    <HTML>
    <BODY>
    <H1>2nd Web Server. It works! :-)</H1>
    </BODY>
    </HTML>

El funcionamiento es correcto.

### Apache Benchmark

En este punto, vamos a usar la herramienta **ab** para someter a una carga elevada nuestro LB server, incluyendo un gran número de peticiones con alta concurrencia.

En el node2 (LB server), he programado un pequeño script como ayuda a realizar esta comparativa:

        -------------------------------
        TARSOT'S LOAD BALANCER SERVICES
            Node0, 1, 2 (LB), 3, 4      
        -------------------------------

        Choose your option:

      1) Start Nginx    | 2) Stop Nginx
      3) Start HaProxy  | 4) Stop HaProxy
      5) Start Pound    | 6) Stop Pound

      9) Test port 
      0) Exit

      Option: 


#### Nginx

    vagrant@node4:~$ ab -n 100000 -c 500 node2/index.html

    Benchmarking node2 (be patient)
    Completed 10000 requests
    Completed 20000 requests
    Completed 30000 requests
    Completed 40000 requests
    Completed 50000 requests
    Completed 60000 requests
    Completed 70000 requests
    Completed 80000 requests
    Completed 90000 requests
    Completed 100000 requests
    Finished 100000 requests


    Server Software:        nginx/1.1.19
    Server Hostname:        node2
    Server Port:            80

    Document Path:          /index.html
    Document Length:        69 bytes

    Concurrency Level:      500
    Time taken for tests:   59.894 seconds
    Complete requests:      100000
    Failed requests:        0
    Write errors:           0
    Total transferred:      33150000 bytes
    HTML transferred:       6900000 bytes
    Requests per second:    1669.62 [#/sec] (mean)
    Time per request:       299.470 [ms] (mean)
    Time per request:       0.599 [ms] (mean, across all concurrent requests)
    Transfer rate:          540.51 [Kbytes/sec] received

    Connection Times (ms)
                  min  mean[+/-sd] median   max
    Connect:        0   74 186.4     40    3068
    Processing:    16  222 236.2    175    3741
    Waiting:        9  209 236.4    159    3737
    Total:         29  296 298.7    217    4219

#### HAProxy

    vagrant@node4:~$ ab -n 100000 -c 500 node2/index.html

    Benchmarking node2 (be patient)
    Completed 10000 requests
    Completed 20000 requests
    Completed 30000 requests
    Completed 40000 requests
    Completed 50000 requests
    Completed 60000 requests
    Completed 70000 requests
    Completed 80000 requests
    Completed 90000 requests
    Completed 100000 requests
    Finished 100000 requests


    Server Software:        Apache/2.2.22
    Server Hostname:        node2
    Server Port:            80

    Document Path:          /index.html
    Document Length:        69 bytes

    Concurrency Level:      500
    Time taken for tests:   93.343 seconds
    Complete requests:      100000
    Failed requests:        0
    Write errors:           0
    Total transferred:      34099960 bytes
    HTML transferred:       6900000 bytes
    Requests per second:    1071.32 [#/sec] (mean)
    Time per request:       466.715 [ms] (mean)
    Time per request:       0.933 [ms] (mean, across all concurrent requests)
    Transfer rate:          356.76 [Kbytes/sec] received

    Connection Times (ms)
                  min  mean[+/-sd] median   max
    Connect:        0  134 451.0      4   15082
    Processing:    19  331  97.8    315    3803
    Waiting:       19  329  97.7    312    3801
    Total:         34  465 472.0    321   15628


#### Pound


    vagrant@node4:~$ ab -n 100000 -c 500 node2/index.html

    Benchmarking node2 (be patient)
    Completed 10000 requests
    Completed 20000 requests
    Completed 30000 requests
    Completed 40000 requests
    Completed 50000 requests
    Completed 60000 requests
    Completed 70000 requests
    Completed 80000 requests
    Completed 90000 requests
    Completed 100000 requests
    Finished 100000 requests


    Server Software:        Apache/2.2.22
    Server Hostname:        node2
    Server Port:            80

    Document Path:          /index.html
    Document Length:        69 bytes

    Concurrency Level:      500
    Time taken for tests:   93.343 seconds
    Complete requests:      100000
    Failed requests:        0
    Write errors:           0
    Total transferred:      34099960 bytes
    HTML transferred:       6900000 bytes
    Requests per second:    1071.32 [#/sec] (mean)
    Time per request:       466.715 [ms] (mean)
    Time per request:       0.933 [ms] (mean, across all concurrent requests)
    Transfer rate:          356.76 [Kbytes/sec] received

    Connection Times (ms)
                  min  mean[+/-sd] median   max
    Connect:        0  134 451.0      4   15082
    Processing:    19  331  97.8    315    3803
    Waiting:       19  329  97.7    312    3801
    Total:         34  465 472.0    321   15628

Según la comparativa, Nginx se posiciona con mejores tiempos medios de servicio vs. resto de balanceadores. Es perfectamente funcional, fiable y escalable.

Sin embargo, para un balanceador de carga real, con alta disponibilidad, monitorización y funcionalidad completa de entrega de aplicaciones, quizá debamos utilizar HAProxy.
