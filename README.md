Reservas App

Aplicación para gestión de reservas (Trabajo Práctico).

## Integración del repositorio
- Fork de backend y frontend integrados como código plano.


## Arquitectura
navegador ──► reservas-frontend ──────► reservas-api ──────► reservas-db
React + Vite Node 20 + Express MySQL 8.4
nginx :8080 :3000 :3306
(host 3000) (host 3001, solo (sin puerto
depuración) publicado)
| Capa | Imagen | Puerto interno | Puerto publicado |
| --- | --- | --- | --- |
| `reservas-frontend` | React + Vite servido por nginx | 8080 | 3000 |
| `reservas-api` | Node 20 + Express | 3000 | 3001 (solo depuración) |
| `reservas-db` | MySQL 8.4 | 3306 | — |
El frontend es el único punto de entrada: nadie le habla a la base
directamente, y a la API le habla el frontend.


## Resolucion de consignas 
## 3) Ejecutar deliberadamente el motor de base de datos sin config alguna.
```docker run -d --name reservas-db mysql:8.4 ```   

### diagnostico de contenedor sin configuracion


``` estudiante@curso-docker:~/Documentos/reservas-app$ docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
estudiante@curso-docker:~/Documentos/reservas-app$ docker run -d --name reservas-db mysql:8.4
d2c6665ea6b93d921c946345b3c48fd7a2e9828500a2b9da4a1ab4bb1cca82b9
```
```a) la salida de docker ps -a , que mostrara el contenedor en estado Exited ```
estudiante@curso-docker:~/Documentos/reservas-app$ docker ps -a
CONTAINER ID   IMAGE       COMMAND                  CREATED              STATUS                          PORTS     NAMES
d2c6665ea6b9   mysql:8.4   "docker-entrypoint.s…"   About a minute ago   Exited (1) About a minute ago             reservas-db

``` b)la linea exacta del error informado por docker logs reservas-db ```
estudiante@curso-docker:~/Documentos/reservas-app$ docker logs reservas-db
2026-09-24 20:09:17+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.4.11-1.el9 started.
2026-09-24 20:09:18+00:00 [Note] [Entrypoint]: Switching to dedicated user 'mysql'
2026-09-24 20:09:18+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.4.11-1.el9 started.
2026-09-24 20:09:18+00:00 [ERROR] [Entrypoint]: Database is uninitialized and password option is not specified
    You need to specify one of the following as an environment variable: ```
 - MYSQL_ROOT_PASSWORD 
 - MYSQL_ALLOW_EMPTY_PASSWORD 
 - MYSQL_RANDOM_ROOT_PASSWORD 

``` c) una explicacion, en dos lineas, de la causa de dicho error.```
 Requiere obligatoriamente una clave para el usuario root al iniciar por primera vez el programa ```


## 4) Consultar la documentacion oficial de la imagen mysql en docker hub y determinar cuales son las variables de entorno que la imagen espera recibir.



``` la variable de entorno que es mandatoria y servira para que desaparezca el error es la siguiente  
- MYSQL_ROOT_PASSWORD: Define la password del superusuario root, Si se inicia la imagen sin este sin esta variable (o sin una de sus alternativas), el contenedor falla  se cierra.
esas alternativas son : 
- MYSQL_ALLOW_EMPTY_PASSWORD=yes,permite inicializar el servidor con la contraseña de root en blanco, o vacia.
- MYSQL_RANDOM_ROOT_PASSWORD=yes,genera una contraseña aleatoria para root al iniciar y la muestra en la salida de docker logs.

las siguientes variables no son estrictamente obligatorias para que levante el contenedor por si solo, pero se configuran para crear la base de datos y el usuario de la aplicacion en el primer arranque.

- MYSQL_DATABASE: Crea una base de datos al arrancar(reservasdb).
- MYSQL_USER: Crea un usuario secundario sin privilegios de root (reservas_user).
- MYSQL_PASSWORD: Le asigna la contraseña a dicho usuario secundario (reservas_paeservasss).


#######################################################
ejecutar nuevamente el contenedor acompañando los valores estandar de la variante, en mi caso coloque las variables de entorno en un archivo .env

``` docker run -d --name reservasdb --env-files .env mysql:8.4

A continuacion se muestra la salida del comando docker logs -f reservasdb

studiante@curso-docker:~/Documentos/reservas-app$ docker logs -f reservasdb
2026-09-25 00:43:17+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.4.11-1.el9 started.
2026-09-25 00:43:18+00:00 [Note] [Entrypoint]: Switching to dedicated user 'mysql'
2026-09-25 00:43:18+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.4.11-1.el9 started.
2026-09-25 00:43:18+00:00 [Note] [Entrypoint]: Initializing database files
2026-09-25T00:43:18.304437Z 0 [System] [MY-015017] [Server] MySQL Server Initialization - start.
2026-09-25T00:43:18.305652Z 0 [System] [MY-013169] [Server] /usr/sbin/mysqld (mysqld 8.4.11) initializing of server in progress as process 79
2026-09-25T00:43:18.311936Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2026-09-25T00:43:18.623557Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
2026-09-25T00:43:19.671011Z 6 [Warning] [MY-010453] [Server] root@localhost is created with an empty password ! Please consider switching off the --initialize-insecure option.
2026-09-25T00:43:21.543105Z 0 [System] [MY-015018] [Server] MySQL Server Initialization - end.
2026-09-25 00:43:21+00:00 [Note] [Entrypoint]: Database files initialized
2026-09-25 00:43:21+00:00 [Note] [Entrypoint]: Starting temporary server
2026-09-25T00:43:21.583494Z 0 [System] [MY-015015] [Server] MySQL Server - start.
2026-09-25T00:43:21.820853Z 0 [System] [MY-010116] [Server] /usr/sbin/mysqld (mysqld 8.4.11) starting as process 118
2026-09-25T00:43:21.848585Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2026-09-25T00:43:22.204329Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
2026-09-25T00:43:22.485917Z 0 [Warning] [MY-010068] [Server] CA certificate ca.pem is self signed.
2026-09-25T00:43:22.485939Z 0 [System] [MY-013602] [Server] Channel mysql_main configured to support TLS. Encrypted connections are now supported for this channel.
2026-09-25T00:43:22.489108Z 0 [Warning] [MY-011810] [Server] Insecure configuration for --pid-file: Location '/var/run/mysqld' in the path is accessible to all OS users. Consider choosing a different directory.
2026-09-25T00:43:22.513045Z 0 [System] [MY-011323] [Server] X Plugin ready for connections. Socket: /var/run/mysqld/mysqlx.sock
2026-09-25T00:43:22.513099Z 0 [System] [MY-010931] [Server] /usr/sbin/mysqld: ready for connections. Version: '8.4.11'  socket: '/var/run/mysqld/mysqld.sock'  port: 0  MySQL Community Server - GPL.
2026-09-25 00:43:22+00:00 [Note] [Entrypoint]: Temporary server started.
'/var/lib/mysql/mysql.sock' -> '/var/run/mysqld/mysqld.sock'
2026-09-25 00:43:23+00:00 [Note] [Entrypoint]: Creating database reservasdb
2026-09-25 00:43:23+00:00 [Note] [Entrypoint]: Creating user reservas_user
2026-09-25 00:43:23+00:00 [Note] [Entrypoint]: Giving user reservas_user access to schema reservasdb

2026-09-25 00:43:23+00:00 [Note] [Entrypoint]: Stopping temporary server
2026-09-25T00:43:23.797396Z 13 [System] [MY-013172] [Server] Received SHUTDOWN from user root. Shutting down mysqld (Version: 8.4.11).
2026-09-25T00:43:24.898855Z 0 [System] [MY-010910] [Server] /usr/sbin/mysqld: Shutdown complete (mysqld 8.4.11)  MySQL Community Server - GPL.
2026-09-25T00:43:24.898872Z 0 [System] [MY-015016] [Server] MySQL Server - end.
2026-09-25 00:43:25+00:00 [Note] [Entrypoint]: Temporary server stopped

2026-09-25 00:43:25+00:00 [Note] [Entrypoint]: MySQL init process done. Ready for start up.

2026-09-25T00:43:25.842155Z 0 [System] [MY-015015] [Server] MySQL Server - start.
2026-09-25T00:43:26.084708Z 0 [System] [MY-010116] [Server] /usr/sbin/mysqld (mysqld 8.4.11) starting as process 1
2026-09-25T00:43:26.090423Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2026-09-25T00:43:26.426247Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
2026-09-25T00:43:26.617393Z 0 [Warning] [MY-010068] [Server] CA certificate ca.pem is self signed.
2026-09-25T00:43:26.617416Z 0 [System] [MY-013602] [Server] Channel mysql_main configured to support TLS. Encrypted connections are now supported for this channel.
2026-09-25T00:43:26.620339Z 0 [Warning] [MY-011810] [Server] Insecure configuration for --pid-file: Location '/var/run/mysqld' in the path is accessible to all OS users. Consider choosing a different directory.
2026-09-25T00:43:26.638752Z 0 [System] [MY-011323] [Server] X Plugin ready for connections. Bind-address: '::' port: 33060, socket: /var/run/mysqld/mysqlx.sock
2026-09-25T00:43:26.638802Z 0 [System] [MY-010931] [Server] /usr/sbin/mysqld: ready for connections. Version: '8.4.11'  socket: '/var/run/mysqld/mysqld.sock'  port: 3306  MySQL Community Server - GPL.

``` 

## 5) Verificar el estado de la base desde el interior del contenedor mediante el cliente mysql que la propia provee.

### a) Ejecutar SHOW DATABASES ###
### b) Ejecutar SHOW TABLES ###
### c) Ejecutar SHOW DATABASES ###
```
docker exec -it reservasdb mysql -u root -preservas_root_pass

estudiante@curso-docker:~/Documentos/reservas-app$ docker exec -it reservasdb mysql -u root -preservas_root_pass
mysql: [Warning] Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 8
Server version: 8.4.11 MySQL Community Server - GPL

Copyright (c) 2000, 2026, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> SHOW DATABASES
    -> ;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| reservasdb         |
| sys                |
+--------------------+
5 rows in set (0.01 sec)

mysql> USE reservasdb;
Database changed
mysql> SHOW TABLES;
Empty set (0.00 sec)

mysql> exit
Bye

``` 
### La Segunda consulta no devuelve nada basicamente por que no hay tablas generadas , y por lo tanto muestra como retorno vacio ###

## 6) ejecutar docker inspect --format '{{json .Config.Env}}' reservasdb 
``` estudiante@curso-docker:~/Documentos/reservas-app$ docker inspect --format '{{json .Config.Env}}' reservasdb
["MYSQL_DATABASE=reservasdb","MYSQL_USER=reservas_user","MYSQL_PASSWORD=reservas_pass","MYSQL_ROOT_PASSWORD=reservas_root_pass","PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin","GOSU_VERSION=1.19","MYSQL_MAJOR=8.4","MYSQL_VERSION=8.4.11-1.el9","MYSQL_SHELL_VERSION=8.4.10-1.el9"]
``` 
- Este comando deja expuesta informacion sensible, consituyendo un problema en la seguridad, punto que habra que resolver mas adelante.



## 7) Comprobar la ausencia de la persistencia

``` docker exec -it reservasdb mysql -u root -preservas_root_pass -e "USE reservasdb; CREATE TABLE prueba (id INT); SHOW TABLES;"

estudiante@curso-docker:~/Documentos/reservas-app$ docker exec -it reservasdb mysql -u root -preservas_root_pass -e "USE reservasdb; CREATE TABLE prueba(id INT); SHOW TABLES;"
mysql: [Warning] Using a password on the command line interface can be insecure.
+----------------------+
| Tables_in_reservasdb |
+----------------------+
| prueba               |
+----------------------+

```

``` 
- Se elimina y se vuelve a crear el contenedor 
- docker rm -f reservasdb

estudiante@curso-docker:~/Documentos/reservas-app$ docker rm -f reservasdb
reservasdb
estudiante@curso-docker:~/Documentos/reservas-app$ docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
estudiante@curso-docker:~/Documentos/reservas-app$  

- docker run -d --name reservasdb --env-file env mysql:8.4

estudiante@curso-docker:~/Documentos/reservas-app$ docker run -d --name reservasdb --env-file env mysql:8.4
1f20814423fe5c8232113b38750ab52f4145c0d45a5c0f4b6088f7bedc8506c3
estudiante@curso-docker:~/Documentos/reservas-app$ docker ps -a
CONTAINER ID   IMAGE       COMMAND                  CREATED         STATUS         PORTS                 NAMES
1f20814423fe   mysql:8.4   "docker-entrypoint.s…"   8 seconds ago   Up 8 seconds   3306/tcp, 33060/tcp   reservasdb
estudiante@curso-docker:~/Documentos/reservas-app$


``` 
``` 
    se espera la inicializacion de la BD , se ejecuta 

    docker logs -f reservasdb y se espera a que aparezca por segunda vez el mensaje ready for connections (port:3306)

2026-09-25T01:57:00.848861Z 0 [System] [MY-015015] [Server] MySQL Server - start.
2026-09-25T01:57:01.160332Z 0 [System] [MY-010116] [Server] /usr/sbin/mysqld (mysqld 8.4.11) starting as process 1
2026-09-25T01:57:01.166894Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2026-09-25T01:57:01.479453Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
2026-09-25T01:57:01.708476Z 0 [Warning] [MY-010068] [Server] CA certificate ca.pem is self signed.
2026-09-25T01:57:01.708501Z 0 [System] [MY-013602] [Server] Channel mysql_main configured to support TLS. Encrypted connections are now supported for this channel.
2026-09-25T01:57:01.711411Z 0 [Warning] [MY-011810] [Server] Insecure configuration for --pid-file: Location '/var/run/mysqld' in the path is accessible to all OS users. Consider choosing a different directory.
2026-09-25T01:57:01.731331Z 0 [System] [MY-011323] [Server] X Plugin ready for connections. Bind-address: '::' port: 33060, socket: /var/run/mysqld/mysqlx.sock
2026-09-25T01:57:01.731516Z 0 [System] [MY-010931] [Server] /usr/sbin/mysqld: ready for connections. Version: '8.4.11'  socket: '/var/run/mysqld/mysqld.sock'  port: 3306  MySQL Community Server - GPL.



``` 
``` 
consulta nuevamente ls tablas:

docker exec -it reservasdb mysql -u root -preservasdb_root_pass -e "USE reservasdb; SHOW TABLES;"

al volver a consultar sale nuevamente el mensaje EMPTY indicando que al eliminar el contenedor se perdieron los datos porque no se usaron volumenes


estudiante@curso-docker:~/Documentos/reservas-app$ docker ps -a
CONTAINER ID   IMAGE       COMMAND                  CREATED          STATUS          PORTS                 NAMES
1f20814423fe   mysql:8.4   "docker-entrypoint.s…"   10 minutes ago   Up 10 minutes   3306/tcp, 33060/tcp   reservasdb
estudiante@curso-docker:~/Documentos/reservas-app$ docker exec -it reservasdb mysql -u root -preservas_root_pass
mysql: [Warning] Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 12
Server version: 8.4.11 MySQL Community Server - GPL

Copyright (c) 2000, 2026, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> USE reservasdb
Database changed
mysql> SHOW TABLES;
Empty set (0.00 sec)

mysql> 




``` 








