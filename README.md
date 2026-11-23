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


# `reservas-app` — Sistema de Reservas de Salas (Variante B)

Proyecto integrador de la cátedra **Contenedores, Docker y Orquestación con Kubernetes (FACET - UNT)**.
Stack tecnológico: **React + Vite** (servido con Nginx sin privilegios), **Node.js 20 + Express** y **MySQL 8.4**.

> **Nota de seguridad:** Las credenciales incluidas en `.env.example`, `k8s/secret-reservas-db.yaml` y `reservas-app/values.yaml` corresponden exclusivamente a valores de laboratorio. En un entorno productivo deben gestionarse mediante un gestor externo de secretos y cifrado en reposo.

---

## Estructura del Repositorio

* `backend/`: Código fuente de la API Node/Express, `.dockerignore` y `Dockerfile` multi-etapa.
* `frontend/`: Código fuente React/Vite, plantilla Nginx (`default.conf.template`), `.dockerignore` y `Dockerfile` multi-etapa.
* `docker-compose.yml` y `.env.example`: Orquestación local completa en una sola máquina.
* `k8s/`: Los 12 manifiestos individuales de Kubernetes.
* `reservas-app/`: Chart de Helm parametrizado con los 12 objetos (`Chart.yaml`, `values.yaml` y `templates/`).
* `ARQUITECTURA.md`: Fundamentación técnica, concurrencia, dificultades de MySQL y brecha hacia producción.
* `INFORME.md` e `informe/`: Evidencias de despliegue, concurrencia, autoescalado y ciclo de vida Helm.

---

## Camino 1: Despliegue en una sola máquina con Docker Compose

1. Crear el archivo de variables de entorno a partir de la plantilla:
   ```bash
   cp .env.example .env




  
## Guía de Despliegue

El proyecto ofrece dos caminos independientes para levantar y verificar la aplicación según el entorno disponible:
### Camino 1: Despliegue en una sola máquina (Docker Compose)

### Este camino permite ejecutar la aplicación completa en un equipo local utilizando únicamente Docker Engine y Docker Compose.

   1. Preparar las variables de entorno:
    Copiar la plantilla de ejemplo para generar el archivo .env local (ignorado en Git):
    Bash

    ``` cp .env.example .env ``` 

   2. Construir y levantar los servicios:
    Bash

   ``` docker compose up -d --build ```

    3. Verificar el estado de los contenedores:
    Confirmar que los tres servicios (reservas-db, reservas-api y reservas-frontend) se encuentren en estado Up (healthy):
    Bash

    ```  docker compose ps ``` 

    Acceder a la aplicación:

        Interfaz Web (Frontend):[http://localhost:3000]

        API REST (Salud / Depuración):[http://localhost:3001/health]   
        

    4. Detener y limpiar el entorno:
    Para detener y eliminar los contenedores conservando el volumen de datos (reservas-db-data):
    Bash

    ```  docker compose down ```

#### Camino 2: Despliegue en un clúster de Kubernetes (Chart de Helm)

Este camino despliega los 12 objetos de la arquitectura completa en Kubernetes (alta disponibilidad, persistencia mediante PVC, autoescalado HPA y enrutamiento por Ingress) utilizando el chart empaquetado en reservas-app/.   


1. Validar el renderizado de las plantillas antes de instalar:
    Comprobar que Helm genera correctamente los 12 manifiestos con los valores por defecto:   
    
   ```  helm template reservas ./reservas-app ``` 

2. Instalar la release en el clúster:
    Desplegar la aplicación creando automáticamente el namespace reservas-app:   

   ```  helm install reservas ./reservas-app -n reservas-app --create-namespace ``` 

3. Verificar los recursos desplegados:
    Comprobar que todos los Pods estén en estado Running, el PVC en Bound y el HPA reportando métricas de CPU:   

  ```  kubectl get all,cm,secret,pvc,ingress,hpa -n reservas-app ``` 

4. Configurar la resolución DNS local para el Ingress:
    Obtener la EXTERNAL-IP asignada al controlador Ingress y asociarla al dominio reservas.local:   

   ```INGRESS_IP=$(kubectl get svc -n nginx-ingress nginx-ingress-controller -o jsonpath='{.status.loadBalancer.ingress[0].ip}') ```
    ```  echo "$INGRESS_IP reservas.local" | sudo tee -a /etc/hosts``` 

5. Acceder y probar la aplicación:

 Interfaz Web (Frontend): [http://reservas.local](http://reservas.local)   


Verificación de API a través del proxy: curl -s [http://reservas.local/api/reservas](http://reservas.local/api/reservas)

(Nota de arquitectura: No se define una regla /api en el objeto Ingress porque el servidor Nginx dentro de reservas-frontend ya actúa como proxy inverso hacia http://reservas-api:3000/, manteniendo una única fuente de verdad y paridad con Docker Compose).

6. Desinstalar la release (opcional):

   ```  helm uninstall reservas -n reservas-app ``` 
 
Inventario de los 12 Objetos de Kubernetes

El despliegue completo en Kubernetes (tanto en k8s/ como en el chart reservas-app/) está compuesto exactamente por los siguientes 12 recursos:   

#	Tipo de Objeto	Nombre	Función dentro de la arquitectura
1.	ConfigMap	reservas-api-config	

Define parámetros no sensibles de la API (DB_HOST, DB_PORT, DB_NAME, PORT, SEED_DEMO).

2.	ConfigMap	reservas-frontend-config	

Define el host y puerto del backend (API_HOST, API_PORT) consumidos por Nginx al iniciar.

3.	Secret	reservas-db-secret	

Almacena credenciales (MYSQL_USER, MYSQL_PASSWORD, MYSQL_ROOT_PASSWORD) inyectadas en MySQL y la API.

4.	PersistentVolumeClaim	reservas-db-pvc	

Solicita 1Gi de almacenamiento persistente (ReadWriteOnce) montado con subPath: mysql.

5.	Service	reservas-db	

Expone internamente (ClusterIP) el motor MySQL en el puerto 3306.

6.	Service	reservas-api	

Expone internamente (ClusterIP) el servicio backend Express en el puerto 3000.

7.	Service	reservas-frontend	

Expone el servidor web Nginx en el puerto 8080 (NodePort 30301).

8.	Deployment	reservas-db	

Gestiona 1 réplica de mysql:8.4 vinculada al volumen persistente reservas-db-pvc.

9.	Deployment	reservas-api	

Gestiona las réplicas de luisv3k/reservas-api con resources.requests.cpu configurado.

10.	Deployment	reservas-frontend	

Gestiona 2 réplicas de luisv3k/reservas-frontend sirviendo la SPA y el proxy /api/.

11.	Ingress	reservas-app-ingress	

Enruta el tráfico HTTP entrante del host reservas.local (/) hacia reservas-frontend:8080.

12.	HorizontalPodAutoscaler	reservas-api-hpa	

Escala automáticamente reservas-api entre 2 y 6 réplicas al superar el 50% de CPU.
