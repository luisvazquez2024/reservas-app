# Arquitectura del Sistema — `reservas-app` (Variante B)

## 1. Preguntas Generales del Curso

### a) Configuración del frontend en tiempo de ejecución (`API_HOST`) vs. tiempo de construcción (`VITE_API_URL`)
En una aplicación Vite/React, las variables con prefijo `VITE_*` se incrustan estáticamente dentro del bundle JavaScript durante `npm run build`. Si se utilizara `VITE_API_URL`, la dirección del backend quedaría fija dentro de los archivos estáticos compilados y la imagen dejaría de ser portable entre entornos (desarrollo local, Docker Compose, staging o Kubernetes con distintos nombres de Service).

En esta arquitectura, el frontend se compila una única vez como artefacto agnóstico y delega el enrutamiento al servidor Nginx del propio contenedor (`nginxinc/nginx-unprivileged:1.27-alpine`). Al arrancar, el script `/docker-entrypoint.d/20-envsubst-on-templates.sh` sustituye `${API_HOST}` y `${API_PORT}` en `/etc/nginx/templates/default.conf.template` para generar `/etc/nginx/conf.d/default.conf`. Si se hubiera hecho en tiempo de construcción, pasar de desarrollo a producción obligaría a **recompilar el código fuente y construir y publicar una nueva imagen de contenedor** por cada entorno distinto.

### b) Ausencia de configuración CORS en la arquitectura
El navegador carga la interfaz web desde `http://reservas.local` (o `http://localhost:3000` en Compose) y realiza las peticiones a rutas relativas `/api/...` contra el **mismo origen** (esquema, host y puerto idénticos). Nginx intercepta el prefijo `/api/` y actúa como proxy inverso interno hacia `http://reservas-api:3000/`, eliminando el prefijo mediante la barra final de `proxy_pass`. Como el navegador nunca realiza peticiones cruzadas (*cross-origin*), la política SOP (*Same-Origin Policy*) no bloquea el tráfico y no se requiere configurar CORS en Express.

Si la SPA se comunicara directamente contra la API en otro origen o puerto (por ejemplo, `http://api.reservas.local` o `:30501`), habría que configurar en el backend Node/Express el middleware `cors` (cabeceras `Access-Control-Allow-Origin` restringidas al dominio del frontend, `Access-Control-Allow-Methods` y manejo de peticiones *preflight* `OPTIONS`).

### c) Un solo `HorizontalPodAutoscaler` entre los doce objetos
* **Por qué `reservas-api` sí tiene HPA:** Es una capa sin estado (*stateless*). Cada réplica procesa peticiones HTTP de forma independiente y delega el estado persistente en MySQL, por lo que escalar de 2 a 6 réplicas frente a picos de CPU reparte la carga horizontalmente sin inconsistencias.
* **Qué ocurriría si se asignara un HPA a `reservas-db`:** Al desplegarse como un `Deployment` sobre un único `PersistentVolumeClaim` (`ReadWriteOnce`), múltiples réplicas de MySQL intentarían montar y escribir simultáneamente sobre los mismos archivos de datos en `/var/lib/mysql` (corrompiendo tablas InnoDB o fallando por bloqueo de volumen), o bien levantarían instancias aisladas sin replicación entre sí, provocando lecturas inconsistentes según a qué Pod enrute el Service.
* **Por qué `reservas-frontend` tampoco tiene HPA:** El contenedor sirve archivos estáticos compilados (HTML/JS/CSS cacheables) y actúa como proxy ligero con Nginx sobre Alpine, cuyo consumo de CPU por petición es mínimo frente al procesamiento de lógica de negocio y consultas SQL de la API. Con 2 réplicas fijas garantiza alta disponibilidad sin complejidad adicional.

---

## 2. Concurrencia en la Capa de Aplicación (Variante B)

Al escalar `reservas-api` de 1 a 3 réplicas, **deja de estar garantizada la exclusión mutua en memoria a nivel de proceso** entre solicitudes simultáneas. Si la API realiza primero un `SELECT` para verificar disponibilidad antes de ejecutar el `INSERT`, tres réplicas atendiendo peticiones concurrentes para la misma `(sala_id, fecha, franja)` pueden ejecutar el `SELECT` en el mismo instante, leer que la franja está «libre» y enviar las tres la sentencia `INSERT`.

Lo que impide efectivamente la doble reserva es la **restricción de unicidad (`UNIQUE`) definida en el esquema de MySQL sobre `(sala_id, fecha, franja)`**. El motor de base de datos serializa las transacciones en el punto de escritura: acepta el primer `INSERT` (devolviendo `201 Created`) y rechaza los restantes por violación de clave duplicada, error que la API captura y traduce en una respuesta HTTP `409 Conflict` (verificado en `informe/05-concurrencia.txt` con 30 peticiones paralelas: 1 respuesta `201` y 29 respuestas `409`).

---

## 3. Dificultades Propias de MySQL Resueltas en el Curso

1. **Inicialización de MySQL sobre un volumen persistente con contenido previo (`subPath`):**
   * *Manifestación:* Al montar un `PersistentVolumeClaim` previamente utilizado en `/var/lib/mysql`, el Pod `reservas-db` entra en `CrashLoopBackOff`.
   * *Diagnóstico:* Mediante `kubectl logs` se identifica el error `--initialize specified but the data directory has files in it. Aborting.`, ya que la imagen oficial de MySQL exige un directorio vacío para inicializar las tablas del sistema.
   * *Resolución:* Se configuró `subPath: mysql` en el `volumeMount` del Deployment para montar un subdirectorio vacío dentro del mismo PVC sin perder los datos de la aplicación.

2. **Variables de entorno y período de gracia en el `healthcheck` de MySQL (TP 4):**
   * *Manifestación:* En Docker Compose, `reservas-db` quedaba marcado como `unhealthy` durante el primer arranque antes de aceptar conexiones TCP.
   * *Diagnóstico:* Durante la inicialización inicial de InnoDB, MySQL levanta un servidor temporal que solo escucha por socket Unix (`port: 0`) y tarda alrededor de 30–40 segundos en habilitar el puerto `3306`; además, usar `$MYSQL_ROOT_PASSWORD` con un solo `$` provoca que Compose interpole la variable antes de pasarla al contenedor.
   * *Resolución:* Se definió `start_period: 40s` y se ejecutó `mysqladmin ping -h 127.0.0.1 -u root -p"$$MYSQL_ROOT_PASSWORD" --silent` escapando el signo peso (`$$`) para que lo evalúe el shell interno del contenedor.

---

## 4. Brecha hacia un Entorno de Producción Real

Para llevar este despliegue a producción real quedarían pendientes cuatro pilares clave:
1. **Observabilidad:** Recolección centralizada de logs (Loki/ELK), métricas detalladas con Prometheus/Grafana y trazabilidad distribuida, además de sondas `livenessProbe` y `readinessProbe` explícitas en cada Deployment.
2. **CI/CD:** Pipelines automatizados (GitHub Actions / GitLab CI) que ejecuten pruebas, escaneo de vulnerabilidades en imágenes, construcción etiquetada por commit/semver y despliegue continuo (ArgoCD/Flux).
3. **Gestión externa de secretos:** Reemplazar los `Secret` versionados en Git por un gestor dedicado (HashiCorp Vault, AWS Secrets Manager o Sealed Secrets) junto con cifrado en reposo en `etcd`, ya que Base64 es solo codificación y no cifrado.
4. **Copias de respaldo y bases de datos gestionadas:** Implementar backups automáticos periódicos (`mysqldump` / snapshots de volumen) y pruebas de restauración. Un `Deployment` con un PVC `ReadWriteOnce` no se utiliza en producción para bases de datos críticas porque no gestiona identidad de red estable ni replicación ordenada ante fallos de nodo; en su lugar se emplea un servicio gestionado (RDS/Cloud SQL) o un `StatefulSet` con operador especializado y replicación primaria-réplica.
