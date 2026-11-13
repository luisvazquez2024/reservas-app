``` 
1) Crear la red y el volumen con los nombres estándar de la variante, y registrar la salida de
docker network ls y docker volume ls:
docker network create reservas-net
docker volume create reservas-db-data
 
``` 

``` 
    estudiante@curso-docker:~/Documentos/reservas-app$ docker network ls
    NETWORK ID     NAME           DRIVER    SCOPE
    fb32d83867a8   bridge         bridge    local
    4bc0b8594185   host           host      local
    e34c83a861e6   none           null      local
    2751c18a1c8c   reservas-net   bridge    local
    estudiante@curso-docker:~/Documentos/reservas-app$ 

95ed3d07ef9f7917924c8038772fb525a74079089043f7820f19832465afddc1


estudiante@curso-docker:~/Documentos/reservas-app$ docker volume create reservas-db-data
reservas-db-data
estudiante@curso-docker:~/Documentos/reservas-app$ docker volume ls
DRIVER    VOLUME NAME
local     95ed3d07ef9f7917924c8038772fb525a74079089043f7820f19832465afddc1
local     reservas-db-data
estudiante@curso-docker:~/Documentos/reservas-app$ docker network ls
NETWORK ID     NAME           DRIVER    SCOPE
fb32d83867a8   bridge         bridge    local
4bc0b8594185   host           host      local
e34c83a861e6   none           null      local
2751c18a1c8c   reservas-net   bridge    local

``` 
```
2) Ejecutar el contenedor de la base de datos con las características que se detallan a
continuación, sin publicar ningún puerto. El comando debe construirse a partir de lo visto
en clase; se solicita transcribirlo en el README.md junto con su resultado.
Nombre: reservas-db
Red: reservas-net
Imagen: mysql:8.4
Variables de entorno: las cuatro de la primera semana, con los valores estándar de la
variante: base reservasdb, usuario reservas_user, contraseña reservas_pass y
contraseña de root reservas_root_pass
Volumen: reservas-db-data, montado sobre el directorio de datos del motor:
/var/lib/mysql
Puertos publicados: ninguno
La ausencia del parámetro -p es deliberada: la base de datos es la capa más interna y solo
debe alcanzarse desde reservas-net. Corresponde justificar esa decisión por escrito en el
README.md. Adviértase que debe esperarse el mensaje ready for connections que
informa port: 3306, y no el primero, que informa port: 0 (véanse las advertencias).

``` 
```  

``` docker run -d \
  --name reservas-db \
  --network reservas-net \
  -e MYSQL_DATABASE=reservasdb \
  -e MYSQL_USER=reservas_user \
  -e MYSQL_PASSWORD=reservas_pass \
  -e MYSQL_ROOT_PASSWORD=reservas_root_pass \
  -v reservas-db-data:/var/lib/mysql \
  mysql:8.4


  Ausencia de -p : No se publican puertos hacia el anfitrión por seguridad y aislamiento de capas. La base de datos es la capa más interna; solo la API necesita hablar con ella dentro de reservas-net. Exponer el puerto al exterior aumentaría innecesariamente la superficie de ataque.


estudiante@curso-docker:~/Documentos/reservas-app$ docker ps -a
CONTAINER ID   IMAGE       COMMAND                  CREATED          STATUS          PORTS                 NAMES
81336d255dc4   mysql:8.4   "docker-entrypoint.s…"   38 minutes ago   Up 38 minutes   3306/tcp, 33060/tcp   reservas-db


``` 
``` 
`docker logs reservas-db` 

... [Server] /usr/sbin/mysqld: ready for connections. Version: '8.4.x'  socket: '/var/run/mysqld/mysqld.sock'  port: 0  MySQL Community Server - GPL.
...
... [Server] /usr/sbin/mysqld: ready for connections. Version: '8.4.x'  socket: '/var/run/mysqld/mysqld.sock'  port: 3306  MySQL Community Server - GPL.
``` 
``` 
3) Ejecutar el contenedor de la API en la misma red, configurado para alcanzar la base por
nombre. Debe esperarse a que el motor haya terminado de inicializar antes de levantarla.
Nombre: reservas-api
Red: reservas-net
Imagen: reservas-api:v1
Variables de entorno: PORT=3000, DB_HOST=reservas-db, DB_PORT=3306,
DB_NAME=reservasdb, DB_USER=reservas_user, DB_PASSWORD=reservas_pass,
APP_VERSION=v1, SEED_DEMO=true
Puertos publicados: el 3000 del contenedor en el 3001 del anfitrión, solo para
depuración

Comprobar y registrar:

El valor DB_HOST=reservas-db es un nombre, no una dirección IP: lo resuelve el servidor
DNS interno de la red. En la red bridge predeterminada esa resolución no funcionaría.
a)la salida de docker exec reservas-api getent hosts reservas-db;
b)la respuesta de curl -s localhost:3001/health;
C)la salida de docker exec reservas-db mysql -ureservas_user -
preservas_pass reservasdb -e 'SHOW TABLES;', donde ahora sí deben existir
las tablas que la semana 1 no mostraba.


Ejecución y verificación de la API (reservas-api:v1)


docker run -d \
  --name reservas-api \
  --network reservas-net \
  -p 3001:3000 \
  -e PORT=3000 \
  -e DB_HOST=reservas-db \
  -e DB_PORT=3306 \
  -e DB_NAME=reservasdb \
  -e DB_USER=reservas_user \
  -e DB_PASSWORD=reservas_pass \
  -e APP_VERSION=v1 \
  -e SEED_DEMO=true \
  reservas-api:v1


estudiante@curso-docker:~/Documentos/reservas-app$ docker ps -a
CONTAINER ID   IMAGE             COMMAND                  CREATED          STATUS          PORTS                                         NAMES
6d4ac4f013ce   reservas-api:v1   "docker-entrypoint.s…"   17 seconds ago   Up 16 seconds   0.0.0.0:3001->3000/tcp, [::]:3001->3000/tcp reservas-api
81336d255dc4   mysql:8.4         "docker-entrypoint.s…"   41 minutes ago   Up 41 minutes   3306/tcp, 33060/tcp                           reservas-db



estudiante@curso-docker:~/Documentos/reservas-app$ docker exec reservas-api getent hosts reservas-db
172.18.0.2        reservas-db  reservas-db


## Estado de salud de la API

curl -s localhost:3001/health

estudiante@curso-docker:~/Documentos/reservas-app$ curl -s localhost:3001/health
{"status":"ok","db":"ok","version":"v1"}estudiante@curso-docker:~/Documentos/reservas-app$ 

## creacion de tablas en mysql
docker exec reservas-db mysql -ureservas_user -preservas_pass reservasdb -e 'SHOW TABLES;'

{"status":"ok","db":"ok","version":"v1"}estudiante@docker exec reservas-db mysql -ureservas_user -preservas_pass reservasdb -e 'SHOW TABLES;'servas_pass reservasdb -e 'SHOW TABLES;'
mysql: [Warning] Using a password on the command line interface can be insecure.
Tables_in_reservasdb
reservas
salas
estudiante@curso-docker:~/Documentos

``` 

``` 
4) Reproducir el error de manera deliberada: ejecutar el frontend fuera de la red, es decir,
omitiendo el parámetro --network.
- Nombre: reservas-frontend
- Red: ninguna — ahí está el error que se busca
- Imagen: reservas-frontend:v1
- Variables de entorno: API_HOST=reservas-api, API_PORT=3000
- Puertos publicados: el 8080 del contenedor en el 3000 del anfitrión
Registrar la salida de docker ps -a, que informará Exited (1), y la de docker logs
reservas-frontend. Se solicita transcribir el mensaje textual y explicar en dos líneas por
qué el proceso termina en lugar de arrancar en estado degradado: nginx resuelve el
upstream de proxy_pass una única vez, al iniciar, y si no lo resuelve no reintenta.



Punto 4: Reproducción deliberada del error (Frontend fuera de la red)
Comandos
``` 
```

docker run -d \
  --name reservas-frontend \
  -p 3000:8080 \
  -e API_HOST=reservas-api \
  -e API_PORT=3000 \
  reservas-frontend:v1

docker ps -a
docker logs reservas-frontend


estudiante@curso-docker:~/Documentos/reservas-app$ 
docker ps -a
docker logs reservas-frontend

CONTAINER ID   IMAGE                  COMMAND                  CREATED             STATUS                       PORTS                                         NAMES
cc591935c5d9   reservas-frontend:v1   "/docker-entrypoint.…"   13 minutes ago      Exited (1) 13 minutes ago                                                  reservas-frontend
6d4ac4f013ce   reservas-api:v1        "docker-entrypoint.s…"   24 minutes ago      Exited (255) 2 minutes ago   0.0.0.0:3001->3000/tcp, [::]:3001->3000/tcp   reservas-api
81336d255dc4   mysql:8.4              "docker-entrypoint.s…"   About an hour ago   Exited (255) 2 minutes ago   3306/tcp, 33060/tcp                           reservas-db

/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: /etc/nginx/conf.d/default.conf differs from the packaged version
/docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
20-envsubst-on-templates.sh: Running envsubst on /etc/nginx/templates/default.conf.template to /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2026/11/13 18:15:48 [emerg] 1#1: host not found in upstream "reservas-api" in /etc/nginx/conf.d/default.conf:24
nginx: [emerg] host not found in upstream "reservas-api" in /etc/nginx/conf.d/default.conf:24






``` 