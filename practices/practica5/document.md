### Práctica 5. Replicación de bases de datos MySQL

Seguimos con el esquema anterior:


|   **Node0**	|   **Node1**   |   **Node2**   |   **Node3**   | 
| :-----------: | :-----------: | :-----------: | :-----------: | 
| Web Server 1  | Web Server 2  | Load Balancer | Firewall      |  
| 192.168.56.10 | 192.168.56.11 | 192.168.56.12 | 192.168.56.13 |

Comenzamos por crear una BD en MySQL e insertar algunos datos, de modo que luego podamos utilizarlos para realizar las copias de seguridad:

#### Web Server 1 (node0)

	vagrant@node0:~$ mysql -uroot -p                        
	Enter password: 
	Welcome to the MySQL monitor.  Commands end with ; or \g.
	Your MySQL connection id is 46
	Server version: 5.5.62-0ubuntu0.14.04.1 (Ubuntu)

	Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

	Oracle is a registered trademark of Oracle Corporation and/or its
	affiliates. Other names may be trademarks of their respective
	owners.

	Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

	mysql> create database contactos;
	Query OK, 1 row affected (0.00 sec)

	mysql> use contactos;
	Database changed
	mysql> show tables;
	Empty set (0.00 sec)

	mysql> create table datos(nombre varchar(100),tlf int);
	Query OK, 0 rows affected (0.01 sec)

	mysql> show tables;
	+---------------------+
	| Tables_in_contactos |
	+---------------------+
	| datos               |
	+---------------------+
	1 row in set (0.00 sec)

	mysql> insert into datos(nombre,tlf) values ("pepe",95834987);
	Query OK, 1 row affected (0.00 sec)

	mysql> select * from datos;
	+--------+----------+
	| nombre | tlf      |
	+--------+----------+
	| pepe   | 95834987 |
	+--------+----------+
	1 row in set (0.00 sec)

	mysql> describe datos;
	+--------+--------------+------+-----+---------+-------+
	| Field  | Type         | Null | Key | Default | Extra |
	+--------+--------------+------+-----+---------+-------+
	| nombre | varchar(100) | YES  |     | NULL    |       |
	| tlf    | int(11)      | YES  |     | NULL    |       |
	+--------+--------------+------+-----+---------+-------+
	2 rows in set (0.00 sec)

	mysql> flush tables with read lock;
	Query OK, 0 rows affected (0.02 sec)

	mysql> quit
	Bye

#### Replicamos la BD MySQL con *mysqldump*

Una vez bloquedas las tablas, realizamos la copia de seguridad:

	vagrant@node0:~$ mysqldump contactos -u root -p > /tmp/contactos.sql
	Enter password: 

Las desbloqueamos:

	mysql> unlock tables;
	Query OK, 0 rows affected (0.00 sec)

	mysql> quit
	Bye

Copiamos el archivo .SQL a la máquina esclavo (node1, secundaria) que contiene todos los datos guardados desde la máquina principal (node0):

	vagrant@node1:~$ scp node0:/tmp/contactos.sql /tmp/
	contactos.sql   	  100% 1869     1.8KB/s   00:00

	vagrant@node1:/tmp$ cat contactos.sql 
	-- MySQL dump 10.13  Distrib 5.5.62, for debian-linux-gnu (x86_64)
	--
	-- Host: localhost    Database: contactos
	-- ------------------------------------------------------
	-- Server version	5.5.62-0ubuntu0.14.04.1

	/*!40101 SET @OLD_CHARACTER_SET_CLIENT=@@CHARACTER_SET_CLIENT */;
	/*!40101 SET @OLD_CHARACTER_SET_RESULTS=@@CHARACTER_SET_RESULTS */;
	/*!40101 SET @OLD_COLLATION_CONNECTION=@@COLLATION_CONNECTION */;
	/*!40101 SET NAMES utf8 */;
	/*!40103 SET @OLD_TIME_ZONE=@@TIME_ZONE */;
	/*!40103 SET TIME_ZONE='+00:00' */;
	/*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
	/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;
	/*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
	/*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;

	--
	-- Table structure for table `datos`
	--

	DROP TABLE IF EXISTS `datos`;
	/*!40101 SET @saved_cs_client     = @@character_set_client */;
	/*!40101 SET character_set_client = utf8 */;
	CREATE TABLE `datos` (
	  `nombre` varchar(100) DEFAULT NULL,
	  `tlf` int(11) DEFAULT NULL
	) ENGINE=InnoDB DEFAULT CHARSET=latin1;
	/*!40101 SET character_set_client = @saved_cs_client */;

	--
	-- Dumping data for table `datos`
	--

	LOCK TABLES `datos` WRITE;
	/*!40000 ALTER TABLE `datos` DISABLE KEYS */;
	INSERT INTO `datos` VALUES ('pepe',95834987);
	/*!40000 ALTER TABLE `datos` ENABLE KEYS */;
	UNLOCK TABLES;
	/*!40103 SET TIME_ZONE=@OLD_TIME_ZONE */;

	/*!40101 SET SQL_MODE=@OLD_SQL_MODE */;
	/*!40014 SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS */;
	/*!40014 SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS */;
	/*!40101 SET CHARACTER_SET_CLIENT=@OLD_CHARACTER_SET_CLIENT */;
	/*!40101 SET CHARACTER_SET_RESULTS=@OLD_CHARACTER_SET_RESULTS */;
	/*!40101 SET COLLATION_CONNECTION=@OLD_COLLATION_CONNECTION */;
	/*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;

	-- Dump completed on 2019-03-27 16:17:50

Con el archivo de copia de seguridad, en la máquina esclava, podemos importar la BD
completa en el MySQL. Para ello, en un primer paso creamos la BD:

	vagrant@node1:/tmp$ mysql -u root -p
	Enter password: 
	Welcome to the MySQL monitor.  Commands end with ; or \g.
	Your MySQL connection id is 42
	Server version: 5.5.54-0ubuntu0.12.04.1 (Ubuntu)

	Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

	Oracle is a registered trademark of Oracle Corporation and/or its
	affiliates. Other names may be trademarks of their respective
	owners.

	Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

	mysql> create database contactos;
	Query OK, 1 row affected (0.00 sec)

	mysql> show databases;
	+--------------------+
	| Database           |
	+--------------------+
	| information_schema |
	| contactos          |
	| mysql              |
	| performance_schema |
	+--------------------+
	4 rows in set (0.00 sec)

	mysql> quit  
	Bye

Y en un segundo paso, restauramos los datos contenidos en la BD, creándose las tablas en este proceso:

	vagrant@node1:/tmp$ mysql -u root -p contactos < /tmp/contactos.sql
	Enter password: 
	vagrant@node1:/tmp$ mysql -u root -p
	Enter password: 
	Welcome to the MySQL monitor.  Commands end with ; or \g.
	Your MySQL connection id is 45
	Server version: 5.5.54-0ubuntu0.12.04.1 (Ubuntu)

	Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

	Oracle is a registered trademark of Oracle Corporation and/or its
	affiliates. Other names may be trademarks of their respective
	owners.

	Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

	mysql> use contactos;
	Reading table information for completion of table and column names
	You can turn off this feature to get a quicker startup with -A

	Database changed
	mysql> show tables;
	+---------------------+
	| Tables_in_contactos |
	+---------------------+
	| datos               |
	+---------------------+
	1 row in set (0.00 sec)

	mysql> describe datos;
	+--------+--------------+------+-----+---------+-------+
	| Field  | Type         | Null | Key | Default | Extra |
	+--------+--------------+------+-----+---------+-------+
	| nombre | varchar(100) | YES  |     | NULL    |       |
	| tlf    | int(11)      | YES  |     | NULL    |       |
	+--------+--------------+------+-----+---------+-------+
	2 rows in set (0.00 sec)

#### MASTER-SLAVE REPLICATION

Para este proceso, ya partimos de haber clonado las BB.DD. en ambas máquinas (node0 y node1).

Procedemos a laconfiguración de mysql de la principal (node0):

 	vagrant@node0: sudo nano /etc/mysql/my.cnf 

Comentamos el parámetro *bind-address*, que sirve para que escuche a un servidor:

	#bind-address 127.0.0.1

Le indicamos el archivo donde almacenar el log de errores:

	log_error = /var/log/mysql/error.log

Establecemos el identificador del servidor:

	server-id = 1

Activamos el registro binario:

	log_bin = /var/log/mysql/mysql-bin.log

Guardamos el archivo y reiniciamos el servicio:

	vagrant@node0: sudo service mysql restart

En este paso, ya podemos realizar la configuración *mysql* del esclavo (node1). Será análoga a la del maestro (node0), con la diferencia de:

	server-id = 2

En el node0 (maestro), creamos un usuario *esclavo* para que realice la replicación otorgándole los permisos necesarios:

	vagrant@node0:~$ mysql -uroot -p
	Enter password: 
	Welcome to the MySQL monitor.  Commands end with ; or \g.
	Your MySQL connection id is 37
	Server version: 5.5.62-0ubuntu0.14.04.1 (Ubuntu)

	Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

	Oracle is a registered trademark of Oracle Corporation and/or its
	affiliates. Other names may be trademarks of their respective
	owners.

	Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

	mysql> create user esclavo identified by 'esclavo';
	Query OK, 0 rows affected (0.00 sec)

	mysql> GRANT REPLICATION SLAVE ON *.* TO 'esclavo'@'%' IDENTIFIED BY 'esclavo';
	Query OK, 0 rows affected (0.00 sec)

	mysql> flush privileges;                                                       
	Query OK, 0 rows affected (0.00 sec)

	mysql> flush tables;
	Query OK, 0 rows affected (0.01 sec)

	mysql> flush tables with read lock;
	Query OK, 0 rows affected (0.00 sec)

Para finalizar con la configuración en el *maestro*, obtenemos los datos de la BD que
vamos a replicar, para usarlos después en la configuración del *esclavo*:

	mysql> show master status;
	+------------------+----------+--------------+------------------+
	| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB |
	+------------------+----------+--------------+------------------+
	| mysql-bin.000001 |      107 |              |                  |
	+------------------+----------+--------------+------------------+
	1 row in set (0.00 sec)

Volvemos a la máquina *esclava*, entramos en mysql y le damos los datos del *maestro*:

vagrant@node1:~$ mysql -uroot -p

mysql> CHANGE MASTER TO MASTER_HOST='192.168.56.10', MASTER_USER='esclavo', MASTER_PASSWORD='esclavo', MASTER_LOG_FILE='mysql-bin.000001', MASTER_LOG_POS= 107, MASTER_PORT=3306;
Query OK, 0 rows affected (0.15 sec)

Iniciamos el *esclavo*:

	mysql> start slave;
	Query OK, 0 rows affected (0.01 sec)

Desbloqueamos las tablas del *maestro*:

	mysql> UNLOCK TABLES;
	Query OK, 0 rows affected (0.00 sec)

Para asegurarnos de que todo funciona perfectamente y que el *esclavo*
replica la información correctamente, nos vamos al *esclavo* y con la
siguiente orden:

	mysql> SHOW SLAVE STATUS\G

Revisamos si el valor, de la variable “Seconds_Behind_Master”, es distinto de “null”:

	mysql> show slave status\G
	*************************** 1. row ***************************
	               Slave_IO_State: Waiting for master to send event
	                  Master_Host: 192.168.56.10
	                  Master_User: esclavo
	                  Master_Port: 3306
	                Connect_Retry: 60
	              Master_Log_File: mysql-bin.000001
	          Read_Master_Log_Pos: 107
	               Relay_Log_File: mysqld-relay-bin.000002
	                Relay_Log_Pos: 253
	        Relay_Master_Log_File: mysql-bin.000001
	             Slave_IO_Running: Yes
	            Slave_SQL_Running: Yes
	              Replicate_Do_DB: 
	          Replicate_Ignore_DB: 
	           Replicate_Do_Table: 
	       Replicate_Ignore_Table: 
	      Replicate_Wild_Do_Table: 
	  Replicate_Wild_Ignore_Table: 
	                   Last_Errno: 0
	                   Last_Error: 
	                 Skip_Counter: 0
	          Exec_Master_Log_Pos: 107
	              Relay_Log_Space: 410
	              Until_Condition: None
	               Until_Log_File: 
	                Until_Log_Pos: 0
	           Master_SSL_Allowed: No
	           Master_SSL_CA_File: 
	           Master_SSL_CA_Path: 
	              Master_SSL_Cert: 
	            Master_SSL_Cipher: 
	               Master_SSL_Key: 
	        Seconds_Behind_Master: 0 <-- CORRECTO
	Master_SSL_Verify_Server_Cert: No
	                Last_IO_Errno: 0
	                Last_IO_Error: 
	               Last_SQL_Errno: 0
	               Last_SQL_Error: 
	  Replicate_Ignore_Server_Ids: 
	             Master_Server_Id: 1
	1 row in set (0.00 sec)

Para comprobar que todo funciona, vamos al nodo *maestro* (node0) e introducimos nuevos datos en la BD. A continuación, nos dirigimos al *esclavo* para revisar si la modificación se ha reflejado en la tabla modificada en el *maestro*:

	vagrant@node0:~$ mysql -uroot -p

	mysql> use contactos;
	Reading table information for completion of table and column names
	You can turn off this feature to get a quicker startup with -A

	Database changed
	mysql> show tables;
	+---------------------+
	| Tables_in_contactos |
	+---------------------+
	| datos               |
	+---------------------+
	1 row in set (0.00 sec)

	mysql> insert into datos(nombre,tlf) values ("jose",643241570);
	Query OK, 1 row affected (0.01 sec)

	mysql> insert into datos(nombre,tlf) values ("emma",617244572);
	Query OK, 1 row affected (0.01 sec)

	vagrant@node1:~$ mysql -uroot -p

	mysql> select * from datos;
	+--------+-----------+
	| nombre | tlf       |
	+--------+-----------+
	| pepe   |  95834987 |
	| jose   | 643241570 |
	| emma   | 617244572 |
	+--------+-----------+
	3 rows in set (0.01 sec)

Observamos que la información se está replicando correctamente.

#### MASTER-MASTER REPLICATION

Para realizar la configuración maestro-maestro, seguimos el mismo del apartado anterior, haciendo que la máquina 2 (node1) pase a ser *maestra*, igualmente. Además, que la máquina 1 (node0) pase a ser también *esclava*.

Es por lo que, de modo similar, creamos el usuario *esclavo* igual que hicimos en la máquina 2 y le otorgamos los permisos descritos anteriormente:

	vagrant@node1:~$ mysql -uroot -p

	mysql> create user esclavo identified by 'esclavo';
	Query OK, 0 rows affected (0.00 sec)

	mysql> GRANT REPLICATION SLAVE ON *.* TO 'esclavo'@'%' IDENTIFIED BY 'esclavo';
	Query OK, 0 rows affected (0.00 sec)

	mysql> flush privileges;
	Query OK, 0 rows affected (0.00 sec)

	mysql> flush tables;
	Query OK, 0 rows affected (0.01 sec)

	mysql> flush tables with read lock;
	Query OK, 0 rows affected (0.00 sec)

	mysql> show master status;
	+------------------+----------+--------------+------------------+
	| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB |
	+------------------+----------+--------------+------------------+
	| mysql-bin.000001 |      537 |              |                  |
	+------------------+----------+--------------+------------------+
	1 row in set (0.00 sec)

Entonces, en la máquina que fue *maestro* previamente, ejecutamos lo siguiente:

	vagrant@node0:~$ mysql -uroot -p

	CHANGE MASTER TO MASTER_HOST='192.168.56.11', MASTER_USER='esclavo', MASTER_PASSWORD='esclavo', MASTER_LOG_FILE='mysql-bin.000001', MASTER_LOG_POS= 537, MASTER_PORT=3306;

	mysql> start slave;

	mysql> unlock tables;

	mysql> show slave status;

	mysql> select * from datos;

	mysql> insert into datos(nombre,tlf) values ("tarsot",600123456);

	mysql> select * from datos;
	+----------+------------+
	| nombre   | tlf        |
	+----------+------------+
	| pepe     |   95834987 |
	| jose     |  643241570 |
	| emma     |  617244572 |
	| susan    | 2147483647 |
	| peter    |  735144372 |
	| fran     |   61114372 |
	| andrea   |   64114372 |
	| vicent   |  655244372 |
	| tarsot   |  600123456 |
	| knifecut |  600654321 |
	+----------+------------+
	10 rows in set (0.00 sec)

	vagrant@node1:~$ mysql -uroot -p

	mysql> select * from datos;

	mysql> insert into datos(nombre,tlf) values ("knifecut",600654321);

	mysql> select * from datos;
	+----------+------------+
	| nombre   | tlf        |
	+----------+------------+
	| pepe     |   95834987 |
	| jose     |  643241570 |
	| emma     |  617244572 |
	| susan    | 2147483647 |
	| peter    |  735144372 |
	| fran     |   61114372 |
	| andrea   |   64114372 |
	| vicent   |  655244372 |
	| tarsot   |  600123456 |
	| knifecut |  600654321 |
	+----------+------------+
	10 rows in set (0.00 sec)


	En este punto, se da por finalizada la replicación *maestro-maestro*. A partir de ahora, siempre que se modifique la información en alguna, se replicará simultáneamente en la otra base de datos.