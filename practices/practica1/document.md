### Práctica 1. Preparación de las herramientas

Para la realización de todas las prácticas, he desplegado varios nodos con Vagrant: nos ayuda a crear y manejar máquinas virtuales con un mismo entorno de trabajo, así como definir los servicios a instalar.

Por tanto, voy a partir de una *base box*, desde <http://www.vagrantbox.es>, con las siguientes características: 

- Name: envimation/ubuntu-xenial
- Provider: VirtualBox
- URL: <https://atlas.hashicorp.com/envimation/boxes/ubuntu-xenial>
- Size (MB): 133

De este modo, la primera configuración necesaria, será la siguiente:

|   **Node0**   |   **Node1**   |
| ------------- | ------------- |
| Web Server 1  | Web Server 2  | 
| 192.168.56.10 | 192.168.56.11 |


He creado un archivo *Vagrantfile*, que permitirá el despliegue de todos los nodos de la *Web Farm*: 

<img src="Vagrantfile.png" alt="Vagrantfile" width="600" height="300"/>


Al crearse este tipo de entorno, es posible que las máquinas puedan verse entre ellas:

<img src="ping_nodes.png" alt="Ping Nodes" width="600" height="300"/>


Por defecto, Vagrant inicia la máquina virtual sin interfaz gráfica. Sin embargo, podemos acceder a ella mediante SSH con el comando *vagrant ssh*:

<img src="ssh_nodes.png" alt="SSH Nodes" width="500" height="300"/>


Finalizada la instalación, vamos a comprobar la versión del servidor *apache*, viendo que está en ejecución:

<img src="apache_service.png" alt="Apache2 Service" width="450" height="150"/>
<img src="apache2_status.png" alt="Apache2 Status" width="300" height="120"/>

Finalmente, comprobamos la respuesta al comando *curl*, en cada una de las VMs:

<img src="curl_nodes.png" alt="Curl Nodes" width="500" height="250"/>
