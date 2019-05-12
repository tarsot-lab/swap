### Práctica 6. Servidor de disco NFS

Agregamos un nuevo nodo al esquema anterior, con la función de servidor de disco (NFS):


|   **Node0**	|   **Node1**   |   **Node2**   |   **Node3**   |   **Node4**   |
| :-----------: | :-----------: | :-----------: | :-----------: | :-----------: |
| Web Server 1  | Web Server 2  | Load Balancer | Firewall      |      NFS      |
| 192.168.56.10 | 192.168.56.11 | 192.168.56.12 | 192.168.56.13 | 192.168.56.14 |

#### Configuramos el servidor NFS

En la máquina servidora, instalamos las herramientas necesarias:

	vagrant@node4:~$ sudo apt-get install nfs-kernel-server nfs-common rpcbind

Como en todas las VMs, las configuraciones de instalación previas, se recogen en el *script* del arhivo *Vagrantfile*:

	provision :shell, :path => "~/Vagrant/scripts/nodeN.xyz"

	Donde *N*: corresponde al nodo que vayamos a utilizar y *xyz*: al sufijo de la misma.

A continuación, creamos la carpeta que vamos a compartir y cambiamos el propietario
y permisos de esa carpeta:

	vagrant@node4:~$ mkdir /dat/nfserver
	vagrant@node4:~$ sudo chown nobody:nogroup /dat/nfserver/
	vagrant@node4:~$ sudo chmod -R 777 /dat/nfserver/

Para dar permiso de acceso a las máquinas clientes, debemos añadir las IP correspondientes en el archivo de configuración /etc/exports:

	vagrant@node4:~$ /dat/nfserver/ 192.168.56.10(rw) 192.168.56.11(rw)

Finalmente, debemos reiniciar el servicio:

	vagrant@node4:~$ sudo service nfs-kernel-server restart

#### Configuramos los clientes

En los clientes (node0 y node1), instalamos los paquetes necesarios y creamos el punto
de montaje (el directorio en cada máquina cliente):

	$ sudo apt-get install nfs-common rpcbind

	vagrant@node0:~$ \nfsclient0
	vagrant@node1:~$ \nfsclient1

	$ cd /home/usuario
	$ mkdir nfsclient0
	$ chmod -R 777 client0

Montamos la carpeta remota en cada Web Server:

	vagrant@node0:~$ sudo mount node4:/home/vagrant/dat/nfserver nfsclient0/
	vagrant@node1:~$ sudo mount node4:/home/vagrant/dat/nfserver nfsclient1/

Probamos a crear un archivo en cada nodo cliente:

	vagrant@node0:~$ touch nfsclient0/archivo_node0.txt
	vagrant@node1:~$ touch nfsclient1/archivo_node1.txt

Listamos los archivos creados en el servidor de archivos:

	vagrant@node4:~$ ls -la dat/nfserver/
	total 8
	drwxrwxrwx 2 nobody  nogroup 4096 Mar 28 16:54 .
	drwxrwxr-x 3 vagrant vagrant 4096 Mar 28 16:19 ..
	-rw-rw-r-- 1 vagrant vagrant    0 Mar 28 16:54 archivo_node0.txt
	-rw-rw-r-- 1 vagrant vagrant    0 Mar 28 16:54 archivo_node1.txt

Y, finalmente, modificamos el archivo /etc/fstab de los nodos cliente, para que se monte (la carpeta compartida) al iniciar el sistema:

	192.168.56.14:/home/vagrant/dat/nfserver /home/vagrant/nfsclient0/ nfs auto,noatime,nolock,bg,nfsvers=3,intr,tcp,actimeo=1800 0 0
	192.168.56.14:/home/vagrant/dat/nfserver /home/vagrant/nfsclient1/ nfs auto,noatime,nolock,bg,nfsvers=3,intr,tcp,actimeo=1800 0 0

De este modo, cada vez que el sistema arranque (node4), quedará montada la carpeta compartida (nfserver) y será accesible a través de los Web Servers (node0 y node1).