Preparacion previa


docker rm -f reservas-frontend reservas-api reservas-db
docker network rm reservas-net

```  
docker rm -f ...: Detiene y elimina los tres contenedores creados manualmente en el TP 3 para liberar sus nombres y puertos.   
docker network rm reservas-net: Elimina la red manual. 
¿Por qué? Docker Compose etiqueta las redes que crea con com.docker.compose.network. 
Si encuentra una red preexistente sin esa etiqueta, arroja una advertencia o error (network reservas-net was found but has incorrect label...).  
Nota: NO se ejecuta docker volume rm reservas-db-data porque queremos reutilizar el volumen y conservar las reservas guardadas en el TP 3


```
``` 
services:
  reservas-db:
    image: mysql:8.4
    container_name: reservas-db
    environment:
      MYSQL_DATABASE: reservasdb
      MYSQL_USER: reservas_user
      MYSQL_PASSWORD: reservas_pass
      MYSQL_ROOT_PASSWORD: reservas_root_pass
    volumes:
      - reservas-db-data:/var/lib/mysql
    networks:
      - reservas-net
    cap_drop:
      - ALL
    cap_add:
      - CHOWN
      - SETUID
      - SETGID
      - DAC_OVERRIDE
    healthcheck:
      test: ["CMD-SHELL", "mysqladmin ping -h 127.0.0.1 -u root -p\"$$MYSQL_ROOT_PASSWORD\" --silent"]
      interval: 5s
      timeout: 5s
      retries: 20
      start_period: 40s

  reservas-api:
    image: reservas-api:v1
    build: ./api # Ajustar ruta si el Dockerfile está en otra carpeta
    container_name: reservas-api
    ports:
      - "3001:3000"
    environment:
      PORT: "3000"
      DB_HOST: reservas-db
      DB_PORT: "3306"
      DB_NAME: reservasdb
      DB_USER: reservas_user
      DB_PASSWORD: reservas_pass
      APP_VERSION: v1
      SEED_DEMO: "true"
    networks:
      - reservas-net
    cap_drop:
      - ALL
    depends_on:
      reservas-db:
        condition: service_healthy
    healthcheck:
      test: ["CMD", "node", "-e", "fetch('http://127.0.0.1:3000/health').then(r => process.exit(r.ok ? 0 : 1)).catch(() => process.exit(1))"]
      interval: 5s
      timeout: 5s
      retries: 10
      start_period: 10s

  reservas-frontend:
    image: reservas-frontend:v1
    build: ./frontend # Ajustar ruta si el Dockerfile está en otra carpeta
    container_name: reservas-frontend
    ports:
      - "3000:8080"
    environment:
      API_HOST: reservas-api
      API_PORT: "3000"
    networks:
      - reservas-net
    cap_drop:
      - ALL
    depends_on:
      reservas-api:
        condition: service_healthy
    healthcheck:
      test: ["CMD-SHELL", "wget -q --spider http://127.0.0.1:8080/healthz || exit 1"]
      interval: 5s
      timeout: 5s
      retries: 10
      start_period: 5s

networks:
  reservas-net:
    name: reservas-net

volumes:
  reservas-db-data:
    name: reservas-db-data
    external: false


``` 

```

Red y Volumen con name: explícito 
(Punto 1):Por defecto, Compose antepone el nombre de la carpeta del proyecto (ej. reservas-app_reservas-db-data). Al declarar name: reservas-db-data y name: reservas-net, forzamos a Compose a usar exactamente los nombres estándar del TP 3 y a reutilizar el volumen con los datos existentes.  
Particularidades del Healthcheck de MySQL 
(Punto 2):¿Por qué -h 127.0.0.1 y no -h localhost?
Durante la primera inicialización, MySQL levanta un servidor temporal sin red que solo escucha por socket Unix (localhost). Si se usa localhost, mysqladmin ping responde exitoso antes de que la base de datos y los usuarios estén listos. 
Al forzar -h 127.0.0.1, el cliente usa TCP, que solo responde cuando el servidor definitivo está activo. 
¿Por qué $$MYSQL_ROOT_PASSWORD con doble signo pesos? Con un solo $, Docker Compose interpola la variable al leer el docker-compose.yml, dejando la contraseña en texto plano dentro de los metadatos del contenedor (visible con docker inspect). 
Con $$, Compose pasa un $ literal hacia adentro del contenedor y es el shell interno (CMD-SHELL) quien resuelve la variable en tiempo de ejecución.   
¿Por qué start_period: 40s? La creación inicial de tablas de sistema e InnoDB en MySQL es pesada. Este período de gracia evita que los fallos de ping durante los primeros 40 segundos cuenten contra el límite de retries y marquen al contenedor prematuramente como unhealthy.   Healthcheck de la API sin curl 
(Punto 3):Se utiliza el propio intérprete de Node (node -e "fetch(...)") porque Node >= 18 ya incluye la API fetch de forma nativa. ¿Por qué no instalar curl? Instalar paquetes adicionales (apt-get install curl o apk add curl) aumenta el tamaño final de la imagen, ralentiza el build y, lo más crítico en seguridad, amplía la superficie de ataque al introducir binarios innecesarios dentro del contenedor productivo.   Healthcheck del Frontend con wget 
(Punto 4):La imagen del frontend está basada en Alpine Linux, distribución que trae incluida la suite BusyBox con wget preinstalado. Con --spider verifica que /healthz devuelva HTTP 200 sin descargar archivos al disco.   Diferencia entre los dos depends_on 
(Punto 5):reservas-api $\rightarrow$ reservas-db: Requiere obligatoriamente condition: service_healthy. La forma corta (depends_on: [reservas-db]) solo espera a que el proceso PID 1 del contenedor inicie, momento en el cual MySQL todavía está inicializando archivos y rechazaría conexiones. Además, la API implementa reintentos internos (~60s), mecanismo que será indispensable en Kubernetes donde no existe depends_on.   reservas-frontend $\rightarrow$ reservas-api: Para Nginx basta con que el contenedor de la API exista en la red para que el DNS interno resuelva reservas-api al arrancar y evitar el error host not found in upstream (por lo que la forma corta alcanzaría). Sin embargo, usar service_healthy garantiza que Nginx no reciba tráfico HTTP antes de que Express esté listo, evitando errores 502 Bad Gateway en los primeros segundos.
``` 