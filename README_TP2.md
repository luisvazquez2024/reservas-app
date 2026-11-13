Reservas App

Aplicación para gestión de reservas (Trabajo Práctico N° 2) .


``` 
Consignas
1) La imagen de la API (backend/Dockerfile). Escribir un primer Dockerfile ingenuo —
una sola etapa, base node:20 completa, COPY . . seguido de npm install— y medirlo.
Registrar la salida de docker build y de docker images reservas-api. El valor
obtenido es el punto de comparación de todo el práctico --

`docker build -f backend/Dockerfile.naive -t reservas-api:ingenua ./backend`
--
[+] Building 21.9s (10/10) FINISHED                                                                                                                                                                           docker:default
 => [internal] load build definition from Dockerfile.naive                                                                                                                                                              0.0s
 => => transferring dockerfile: 126B                                                                                                                                                                                    0.0s
 => [internal] load metadata for docker.io/library/node:20                                                                                                                                                              1.8s
 => [auth] library/node:pull token for registry-1.docker.io                                                                                                                                                             0.0s
 => [internal] load .dockerignore                                                                                                                                                                                       0.0s
 => => transferring context: 84B                                                                                                                                                                                        0.0s
 => [1/4] FROM docker.io/library/node:20@sha256:8f693eaa7e0a8e71560c9a82b55fd54c2ae920a2ba5d2cde28bac7d1c01c9ba5                                                                                                       17.0s
 => => resolve docker.io/library/node:20@sha256:8f693eaa7e0a8e71560c9a82b55fd54c2ae920a2ba5d2cde28bac7d1c01c9ba5                                                                                                        0.0s
 => => sha256:0b22ec2d9b1a1a0e906e301b5ef08efe7774d5fc001b9cfa08e4e980152a0357 448B / 448B                                                                                                                              0.2s
 => => sha256:2f2ab6e4eb4619f7b8506c38dc79c8adf77acffb7622bc15ceeabb0fbc73b8a1 1.25MB / 1.25MB                                                                                                                          0.5s
 => => sha256:c83419f4430962505175b565eb8a24aafeaa4d441c0b22234e94c6e8160848ba 3.32kB / 3.32kB                                                                                                                          0.7s
 => => sha256:76e6b8204a89d20e7915fdc9a98ff33cb65fd7ce44ecf5a84b6cfca4bdb3d6d0 48.62MB / 48.62MB                                                                                                                        2.0s
 => => sha256:2933ecab0f11302fd71f29aa83ad2904683246f7a8320ad0dc3a60b23f05fee9 211.55MB / 211.55MB                                                                                                                     11.5s
 => => sha256:8fcda2b4d7993820b00c5488d173051e76d01ba6b85620617ba77001b0f9e2fa 64.40MB / 64.40MB                                                                                                                        8.6s
 => => sha256:db53381ee51f9e43304e236099ba097ae1b33ae41a8007e0b6319992eb55fd00 48.49MB / 48.49MB                                                                                                                        4.1s
 => => sha256:e5203b2bfeff92b72e816dc6cbb1f16856f0cd592e521e3c0cfa195a78fe180e 24.04MB / 24.04MB                                                                                                                        1.3s
 => => extracting sha256:db53381ee51f9e43304e236099ba097ae1b33ae41a8007e0b6319992eb55fd00                                                                                                                               1.2s
 => => extracting sha256:e5203b2bfeff92b72e816dc6cbb1f16856f0cd592e521e3c0cfa195a78fe180e                                                                                                                               0.6s
 => => extracting sha256:8fcda2b4d7993820b00c5488d173051e76d01ba6b85620617ba77001b0f9e2fa                                                                                                                               2.6s
 => => extracting sha256:2933ecab0f11302fd71f29aa83ad2904683246f7a8320ad0dc3a60b23f05fee9                                                                                                                               3.7s
 => => extracting sha256:c83419f4430962505175b565eb8a24aafeaa4d441c0b22234e94c6e8160848ba                                                                                                                               0.0s
 => => extracting sha256:76e6b8204a89d20e7915fdc9a98ff33cb65fd7ce44ecf5a84b6cfca4bdb3d6d0                                                                                                                               1.4s
 => => extracting sha256:2f2ab6e4eb4619f7b8506c38dc79c8adf77acffb7622bc15ceeabb0fbc73b8a1                                                                                                                               0.1s
 => => extracting sha256:0b22ec2d9b1a1a0e906e301b5ef08efe7774d5fc001b9cfa08e4e980152a0357                                                                                                                               0.0s
 => [internal] load build context                                                                                                                                                                                       0.0s
 => => transferring context: 252B                                                                                                                                                                                       0.0s
 => [2/4] WORKDIR /app                                                                                                                                                                                                  0.2s
 => [3/4] COPY . .                                                                                                                                                                                                      0.1s
 => [4/4] RUN npm install                                                                                                                                                                                               1.8s
 => exporting to image                                                                                                                                                                                                  0.9s 
 => => exporting layers                                                                                                                                                                                                 0.5s
 => => exporting manifest sha256:487e58d57ddafbcae620109d4635c32d3d1bb20ab1af247b879eb9757acce747                                                                                                                       0.0s
 => => exporting config sha256:7618579baba6f5b4d788b0ea0875cdf0cb63f2ccd4c53f8b621fb10938fba060                                                                                                                         0.0s
 => => exporting attestation manifest sha256:ba78a3622ee44c9130e1323d54b3988aede1965428a68a63fbd4c032dd228b1d                                                                                                           0.0s
 => => exporting manifest list sha256:8297faaca5418542a6f730d92cd98d91425b0fd20ba063fb185d34540c0cb517                                                                                                                  0.0s
 => => naming to docker.io/library/reservas-api:naive                                                                                                                                                                   0.0s
 => => unpacking to docker.io/library/reservas-api:naive                                                                                                                                                                0.3s
`estudiante@curso-docker:~/Documentos/reservas-app$ docker images reservas-api`                                                                                                                          i Info →   U  In Use
IMAGE                ID             DISK USAGE   CONTENT SIZE   EXTRA
reservas-api:naive   8297faaca541       1.59GB          402MB        
```

```
2)Escribir la versión definitiva, multi-etapa, con los siguientes requisitos, que no son
sugerencias:
una etapa deps que instale únicamente las dependencias de producción con npm ci --omit=dev. Adviértase que npm ci respeta el archivo de bloqueo, mientras que npm
install puede resolver otras versiones;
a) el COPY de package.json y package-lock.json antes que el resto del código, de
modo que la capa de instalación salga del cache cuando el manifiesto no cambie;
b) una etapa final con base mínima y ENV NODE_ENV=production;c)
usuario no privilegiado. La imagen oficial de Node ya provee el usuario node: no
corresponde crear uno;
d) ARG APP_VERSION=v1 junto con ENV APP_VERSION=${APP_VERSION};e)
EXPOSE 3000 y CMD en forma de lista.
```
```
# Etapa 1: Dependencias de Producción
FROM node:20-alpine AS deps
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci --omit=dev

# Etapa 2: Imagen Final
FROM node:20-alpine AS runner
WORKDIR /app

ENV NODE_ENV=production
ARG APP_VERSION=v1
ENV APP_VERSION=${APP_VERSION}

COPY --chown=node:node --from=deps /app/node_modules ./node_modules
COPY --chown=node:node . .

USER node

EXPOSE 3000

CMD ["node", "server.js"]

```
3) Crear el archivo backend/.dockerignore con node_modules, .git, .env y npm-
debug.log*. Sin él, el directorio node_modules de la máquina local viaja al contexto de
build, lo vuelve pesado y, en presencia de un COPY . ., puede sobrescribir el que instaló la
imagen —con binarios compilados para otro sistema operativo—.

4) Construir las dos versiones y comprobar que la variable de versión se hornea efectivamente:
docker build -t reservas-api:v1 ./backend
docker build --build-arg APP_VERSION=v2 -t reservas-api:v2 ./backend
docker run --rm --entrypoint sh reservas-api:v2 -c 'echo $APP_VERSION' # debe
imprimir v2
---

` estudiante@curso-docker:~/Documentos/reservas-app$  docker build -t reservas-api:v1 ./backend ` 
` docker build --build-arg APP_VERSION=v2 -t reservas-api:v2 ./backend ` 
`  docker run --rm --entrypoint sh reservas-api:v2 -c 'echo $APP_VERSION' `

[+] Building 1.0s (11/11) FINISHED                                                                                                                                                                            docker:default
 => [internal] load build definition from Dockerfile                                                                                                                                                                    0.0s
 => => transferring dockerfile: 472B                                                                                                                                                                                    0.0s
 => [internal] load metadata for docker.io/library/node:20-alpine                                                                                                                                                       0.6s
 => [internal] load .dockerignore                                                                                                                                                                                       0.0s
 => => transferring context: 84B                                                                                                                                                                                        0.0s
 => [internal] load build context                                                                                                                                                                                       0.0s
 => => transferring context: 252B                                                                                                                                                                                       0.0s
 => [deps 1/4] FROM docker.io/library/node:20-alpine@sha256:fb4cd12c85ee03686f6af5362a0b0d56d50c58a04632e6c0fb8363f609372293                                                                                            0.0s
 => => resolve docker.io/library/node:20-alpine@sha256:fb4cd12c85ee03686f6af5362a0b0d56d50c58a04632e6c0fb8363f609372293                                                                                                 0.0s
 => CACHED [deps 2/4] WORKDIR /app                                                                                                                                                                                      0.0s
 => CACHED [deps 3/4] COPY package.json package-lock.json ./                                                                                                                                                            0.0s
 => CACHED [deps 4/4] RUN npm ci --omit=dev                                                                                                                                                                             0.0s
 => CACHED [runner 3/4] COPY --chown=node:node --from=deps /app/node_modules ./node_modules                                                                                                                             0.0s
 => CACHED [runner 4/4] COPY --chown=node:node . .                                                                                                                                                                      0.0s
 => exporting to image                                                                                                                                                                                                  0.2s
 => => exporting layers                                                                                                                                                                                                 0.0s
 => => exporting manifest sha256:5b4b513fc7ec5a6d20c8a1248c67673bd9f19b557140fdbe2f35930014987387                                                                                                                       0.0s
 => => exporting config sha256:adeee54c7718f3d0918911c8799a2e9d4f8782362d6ed60373fea8eb68f2748b                                                                                                                         0.0s
 => => exporting attestation manifest sha256:7b7e93468feb80873e0f94278986de2d02fc8d7c41050650d2cde7a96bf357ab                                                                                                           0.0s
 => => exporting manifest list sha256:ddf59b71ca7a11895974d3fc5dcab69abd8140ef0facac0f6e7c75ac4beff7e5                                                                                                                  0.0s
 => => naming to docker.io/library/reservas-api:v1                                                                                                                                                                      0.0s
 => => unpacking to docker.io/library/reservas-api:v1                                                                                                                                                                   0.2s
[+] Building 0.5s (11/11) FINISHED                                                                                                                                                                            docker:default
 => [internal] load build definition from Dockerfile                                                                                                                                                                    0.0s
 => => transferring dockerfile: 472B                                                                                                                                                                                    0.0s
 => [internal] load metadata for docker.io/library/node:20-alpine                                                                                                                                                       0.2s
 => [internal] load .dockerignore                                                                                                                                                                                       0.0s
 => => transferring context: 84B                                                                                                                                                                                        0.0s
 => [deps 1/4] FROM docker.io/library/node:20-alpine@sha256:fb4cd12c85ee03686f6af5362a0b0d56d50c58a04632e6c0fb8363f609372293                                                                                            0.0s
 => => resolve docker.io/library/node:20-alpine@sha256:fb4cd12c85ee03686f6af5362a0b0d56d50c58a04632e6c0fb8363f609372293                                                                                                 0.0s
 => [internal] load build context                                                                                                                                                                                       0.0s
 => => transferring context: 252B                                                                                                                                                                                       0.0s
 => CACHED [deps 2/4] WORKDIR /app                                                                                                                                                                                      0.0s
 => CACHED [deps 3/4] COPY package.json package-lock.json ./                                                                                                                                                            0.0s
 => CACHED [deps 4/4] RUN npm ci --omit=dev                                                                                                                                                                             0.0s
 => CACHED [runner 3/4] COPY --chown=node:node --from=deps /app/node_modules ./node_modules                                                                                                                             0.0s
 => CACHED [runner 4/4] COPY --chown=node:node . .                                                                                                                                                                      0.0s
 => exporting to image                                                                                                                                                                                                  0.1s
 => => exporting layers                                                                                                                                                                                                 0.0s
 => => exporting manifest sha256:1389c762ac9927e09bbd75245f8dd0816dd850f1a2c2420e13eecb642665da5b                                                                                                                       0.0s
 => => exporting config sha256:fd899b258fd8d487ca20f929779d9ba83388755672d674ccc5c6fe0b829719bb                                                                                                                         0.0s
 => => exporting attestation manifest sha256:a967868885975f39722f73a5cd7edddb15438c477e8f5c43b8d449f9da4dbece                                                                                                           0.0s
 => => exporting manifest list sha256:4bbf94dfbb86a01a589fbe150dec9231740f5f4a520a4a686aec5e55017ea902                                                                                                                  0.0s
 => => naming to docker.io/library/reservas-api:v2                                                                                                                                                                      0.0s
 => => unpacking to docker.io/library/reservas-api:v2                                                                                                                                                                   0.0s
v2
estudiante@curso-docker:~/Documentos/reservas-app$ --

```
-- Punto 5 y 6: Archivos y construcción de reservas-frontend
1. Archivo frontend/.dockerignore
--
'''
node_modules
dist
.git
.env
npm-debug.log*
'''
-- Evita enviar carpetas pesadas al daemon de Docker y 
-- previene que una compilación dist o node_modules local pise lo que se construye dentro del contenedor
```

```
# Etapa 1: Construcción de assets estáticos
FROM node:20-alpine AS builder
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci
COPY . .
RUN npm run build

# Etapa 2: Servidor Web Nginx sin privilegios
FROM nginxinc/nginx-unprivileged:1.27-alpine

ENV API_HOST=reservas-api \
    API_PORT=3000 \
    NGINX_ENVSUBST_FILTER=API_

# Copiar assets de React
COPY --from=builder /app/dist /usr/share/nginx/html

COPY nginx/templates/default.conf.template /etc/nginx/templates/default.conf.template

EXPOSE 8080
```

```
### Construir y verificar las 3 propiedades del frontend

# Construir la imagen del frontend
docker build -t reservas-frontend:v1 ./frontend

# 7.a) Debe imprimir 'nginx' (confirma usuario distinto de root)
docker run --rm reservas-frontend:v1 whoami

# 7.b) Debe fallar ("node: not found"), demostrando que Node no quedó en la imagen
docker run --rm reservas-frontend:v1 node --version

# 7.c) Registrar el tamaño resultante
docker images reservas-frontend
```

```
estudiante@curso-docker:~/Documentos/reservas-app$ docker build -t reservas-frontend:v1 ./frontend
[+] Building 8.1s (17/17) FINISHED                                                                                                                                                                            docker:default
 => [internal] load build definition from Dockerfile                                                                                                                                                                    0.0s
 => => transferring dockerfile: 556B                                                                                                                                                                                    0.0s
 => [internal] load metadata for docker.io/nginxinc/nginx-unprivileged:1.27-alpine                                                                                                                                      1.9s
 => [internal] load metadata for docker.io/library/node:20-alpine                                                                                                                                                       1.1s
 => [auth] library/node:pull token for registry-1.docker.io                                                                                                                                                             0.0s
 => [auth] nginxinc/nginx-unprivileged:pull token for registry-1.docker.io                                                                                                                                              0.0s
 => [internal] load .dockerignore                                                                                                                                                                                       0.0s
 => => transferring context: 89B                                                                                                                                                                                        0.0s
 => [builder 1/6] FROM docker.io/library/node:20-alpine@sha256:fb4cd12c85ee03686f6af5362a0b0d56d50c58a04632e6c0fb8363f609372293                                                                                         0.1s
 => => resolve docker.io/library/node:20-alpine@sha256:fb4cd12c85ee03686f6af5362a0b0d56d50c58a04632e6c0fb8363f609372293                                                                                                 0.1s
 => [internal] load build context                                                                                                                                                                                       0.1s
 => => transferring context: 92.17kB                                                                                                                                                                                    0.0s
 => [stage-1 1/3] FROM docker.io/nginxinc/nginx-unprivileged:1.27-alpine@sha256:65e3e85dbaed8ba248841d9d58a899b6197106c23cb0ff1a132b7bfe0547e4c0                                                                        2.4s
 => => resolve docker.io/nginxinc/nginx-unprivileged:1.27-alpine@sha256:65e3e85dbaed8ba248841d9d58a899b6197106c23cb0ff1a132b7bfe0547e4c0                                                                                0.1s
 => => sha256:400c1de759f415b6b2a0ba96bcef3e516f26171d5930757dcddc3d6971bf3e81 15.52MB / 15.52MB                                                                                                                        0.8s
 => => sha256:f79056b7935ee1a11822640a4e32333a6fccfc86efddab21fd6d7300edf2d054 1.40kB / 1.40kB                                                                                                                          0.6s
 => => sha256:b16687ec2b7bf8ddce81f21d836d6f9821f1b864f3e8e7fad1734d8295cb4a12 1.20kB / 1.20kB                                                                                                                          0.6s
 => => sha256:8f7ead5f2dc0fb85f6d6b3c81284ccd4b80b783030d202a18ba5ccc5ee98f451 403B / 403B                                                                                                                              0.7s
 => => sha256:048c29a613179205d158f722a495c6b3bbd77439864c075ea5db2d42331d1dcc 956B / 956B                                                                                                                              0.3s
 => => sha256:513bae533eb237e219e2d3412e0dbb9cd94c25b2b84530b5131fda9512c0e773 627B / 627B                                                                                                                              0.2s
 => => sha256:945f5aaec3d7649d815440e6030ec4e4f562488ee50fa779a69f920cab3c5871 2.80kB / 2.80kB                                                                                                                          0.2s
 => => sha256:f18232174bc91741fdf3da96d85011092101a032a93a388b79e99e69c2d5c870 3.64MB / 3.64MB                                                                                                                          0.4s
 => => sha256:576fe098e8467d48ff489eb63f411a74b9e3769957a5c26462c4468a731c3e3d 1.79MB / 1.79MB                                                                                                                          0.3s
 => => extracting sha256:f18232174bc91741fdf3da96d85011092101a032a93a388b79e99e69c2d5c870                                                                                                                               0.1s
 => => extracting sha256:576fe098e8467d48ff489eb63f411a74b9e3769957a5c26462c4468a731c3e3d                                                                                                                               0.1s
 => => extracting sha256:945f5aaec3d7649d815440e6030ec4e4f562488ee50fa779a69f920cab3c5871                                                                                                                               0.0s
 => => extracting sha256:513bae533eb237e219e2d3412e0dbb9cd94c25b2b84530b5131fda9512c0e773                                                                                                                               0.0s
 => => extracting sha256:048c29a613179205d158f722a495c6b3bbd77439864c075ea5db2d42331d1dcc                                                                                                                               0.0s
 => => extracting sha256:8f7ead5f2dc0fb85f6d6b3c81284ccd4b80b783030d202a18ba5ccc5ee98f451                                                                                                                               0.0s
 => => extracting sha256:b16687ec2b7bf8ddce81f21d836d6f9821f1b864f3e8e7fad1734d8295cb4a12                                                                                                                               0.0s
 => => extracting sha256:f79056b7935ee1a11822640a4e32333a6fccfc86efddab21fd6d7300edf2d054                                                                                                                               0.0s
 => => extracting sha256:400c1de759f415b6b2a0ba96bcef3e516f26171d5930757dcddc3d6971bf3e81                                                                                                                               0.4s
 => CACHED [builder 2/6] WORKDIR /app                                                                                                                                                                                   0.0s
 => [builder 3/6] COPY package.json package-lock.json ./                                                                                                                                                                0.1s
 => [builder 4/6] RUN npm ci                                                                                                                                                                                            3.6s
 => [builder 5/6] COPY . .                                                                                                                                                                                              0.1s 
 => [builder 6/6] RUN npm run build                                                                                                                                                                                     1.7s 
 => [stage-1 2/3] COPY --from=builder /app/dist /usr/share/nginx/html                                                                                                                                                   0.0s 
 => [stage-1 3/3] COPY nginx/templates/default.conf.template /etc/nginx/templates/default.conf.template                                                                                                                 0.0s 
 => exporting to image                                                                                                                                                                                                  0.3s 
 => => exporting layers                                                                                                                                                                                                 0.1s 
 => => exporting manifest sha256:e9cd9ab06e6c8069996c0e2165343d696c6be3f87988079fe96210c4b96d432b                                                                                                                       0.0s 
 => => exporting config sha256:e8c9a243100869256a0df1d80404ff39c277832f4c7cd52c2fc5c01a9889cdbf                                                                                                                         0.0s
 => => exporting attestation manifest sha256:a3cc2c462d929206b639af75643044da5841fc3d48ee59f6efd995c398fddfa2                                                                                                           0.0s
 => => exporting manifest list sha256:c2d26a09c4c1a0aa5ec0b8475e506f3d96f580c90fab619bf8973ac426e8e9ab                                                                                                                  0.0s
 => => naming to docker.io/library/reservas-frontend:v1                                                                                                                                                                 0.0s
 => => unpacking to docker.io/library/reservas-frontend:v1   
 ```

 ```
 estudiante@curso-docker:~/Documentos/reservas-app$ docker run --rm reservas-frontend:v1 whoami
nginx
estudiante@curso-docker:~/Documentos/reservas-app$ --

-- estudiante@curso-docker:~/Documentos/reservas-app$ docker run --rm reservas-frontend:v1 node --version
/docker-entrypoint.sh: exec: line 47: node: not found
```

```
estudiante@curso-docker:~/Documentos/reservas-app$ docker images reservas-frontend                                                                                                                                                                                                         i Info →   U  In Use
IMAGE                  ID             DISK USAGE   CONTENT SIZE   EXTRA
reservas-frontend:v1   c2d26a09c4c1       73.9MB           21MB        
estudiante@curso-docker:~/Documentos/reservas-app$ 
```

Punto 9: Inspección de capas con docker history--
```
```
estudiante@curso-docker:~/Documentos/reservas-app$ docker history reservas-api:v1 --format "table {{.ID}}\t{{.CreatedBy}}\t{{.Size}}" | grep -v "0B"
IMAGE          CREATED BY                                      SIZE
<missing>      COPY --chown=node:node . . # buildkit           81.9kB
<missing>      COPY --chown=node:node /app/node_modules ./n…   9.85MB
<missing>      WORKDIR /app                                    8.19kB
<missing>      COPY docker-entrypoint.sh /usr/local/bin/ # …   20.5kB
<missing>      RUN /bin/sh -c apk add --no-cache --virtual …   5.48MB
<missing>      RUN /bin/sh -c addgroup -g 1000 node     && …   130MB
<missing>      ADD alpine-minirootfs-3.23.4-x86_64.tar.gz /…   9.11MB

```
 Identificá la capa que aporta el mayor peso (verás que la capa base de node:20-alpine pesa alrededor de ~130 MB —el runtime de Node.js y el sistema operativo Alpine— y la capa de COPY --from=deps ... /app/node_modules aporta los ~65-75 MB restantes con las dependencias de producción de Express/MySQL).
```

```
Punto 10: Demostración del caché de construcciónEjecutá esta secuencia para comprobar cómo funciona el árbol de caché de Docker:   
```
```
Prueba A — Modificando un archivo de código fuente:
Bash# Agregá un comentario inofensivo al final de un archivo de código del backend
echo "// prueba cache" >> backend/src/index.js

# Reconstruí y observá la salida
docker build -t reservas-api:v1 ./backend
(Qué ocurre: En la salida de la terminal vas a ver CACHED en COPY package.json package-lock.json ./ y en RUN npm ci --omit=dev. Solo se vuelve a ejecutar el COPY del código fuente.   

```
Prueba B — Modificando el manifiesto de dependencias:
# Agregá un espacio al final de package.json (un 'touch' no sirve porque Docker evalúa el contenido, no la fecha)
echo " " >> backend/package.json

# Reconstruí y observá la salida
docker build -t reservas-api:v1 ./backend

Qué ocurre: Se invalida el caché en COPY package.json package-lock.json ./ y Docker se ve obligado a ejecutar nuevamente RUN npm ci --omit=dev desde cero

```
Explicación en dos líneas para el README.md:
Docker calcula un hash sobre el contenido de cada instrucción de forma secuencial: al copiar primero package*.json e instalar dependencias antes que el código fuente, los cambios frecuentes en la lógica de la aplicación no invalidan la capa pesada de node_modules. En cambio, modificar el manifiesto altera su checksum e invalida esa capa y todas las instrucciones posteriores.
```
estudiante@curso-docker:~/Documentos/reservas-app$ # Prueba A: Modificando código fuente real (server.js)
echo "// prueba cache" >> backend/server.js
docker build -t reservas-api:v1 ./backend

# Limpiar el cambio de prueba
git checkout backend/server.js
[+] Building 1.3s (11/11) FINISHED                                                                                                                                                                            docker:default
 => [internal] load build definition from Dockerfile                                                                                                                                                                    0.0s
 => => transferring dockerfile: 472B                                                                                                                                                                                    0.0s
 => [internal] load metadata for docker.io/library/node:20-alpine                                                                                                                                                       0.6s
 => [internal] load .dockerignore                                                                                                                                                                                       0.0s
 => => transferring context: 84B                                                                                                                                                                                        0.0s
 => [deps 1/4] FROM docker.io/library/node:20-alpine@sha256:fb4cd12c85ee03686f6af5362a0b0d56d50c58a04632e6c0fb8363f609372293                                                                                            0.0s
 => => resolve docker.io/library/node:20-alpine@sha256:fb4cd12c85ee03686f6af5362a0b0d56d50c58a04632e6c0fb8363f609372293                                                                                                 0.0s
 => [internal] load build context                                                                                                                                                                                       0.0s
 => => transferring context: 7.11kB                                                                                                                                                                                     0.0s
 => CACHED [deps 2/4] WORKDIR /app                                                                                                                                                                                      0.0s
 => CACHED [deps 3/4] COPY package.json package-lock.json ./                                                                                                                                                            0.0s
 => CACHED [deps 4/4] RUN npm ci --omit=dev                                                                                                                                                                             0.0s
 => CACHED [runner 3/4] COPY --chown=node:node --from=deps /app/node_modules ./node_modules                                                                                                                             0.0s
 => [runner 4/4] COPY --chown=node:node . .                                                                                                                                                                             0.1s
 => exporting to image                                                                                                                                                                                                  0.3s
 => => exporting layers                                                                                                                                                                                                 0.1s
 => => exporting manifest sha256:7b1562d3120fd8d3af99a72ce556d91a4ba49fb8b0ab3d231fa3bb77eddb60ed                                                                                                                       0.0s
 => => exporting config sha256:3f6abc7210e9f98167b9496c34656e24770f3c9967ae24554aa911d513c31ce8                                                                                                                         0.0s
 => => exporting attestation manifest sha256:a676467b52b46c7b8977e0834bbfddfca30afe740c1c8424fe777fd247e4ee17                                                                                                           0.0s
 => => exporting manifest list sha256:216d4ac096c3b4dd551a53d0d4b54aa256d8d41d905c2a947de6f4d2275494ac                                                                                                                  0.0s
 => => naming to docker.io/library/reservas-api:v1                                                                                                                                                                      0.0s
 => => unpacking to docker.io/library/reservas-api:v1                                                                                                                                                                   0.0s
Actualizada 1 ruta desde el índice
---
--
estudiante@curso-docker:~/Documentos/reservas-app$ # Agregá un espacio al final de package.json (un 'touch' no sirve porque Docker evalúa el contenido, no la fecha)
echo " " >> backend/package.json

# Reconstruí y observá la salida
docker build -t reservas-api:v1 ./backend
[+] Building 3.9s (11/11) FINISHED                                                                                                                                                                            docker:default
 => [internal] load build definition from Dockerfile                                                                                                                                                                    0.0s
 => => transferring dockerfile: 472B                                                                                                                                                                                    0.0s
 => [internal] load metadata for docker.io/library/node:20-alpine                                                                                                                                                       0.7s
 => [internal] load .dockerignore                                                                                                                                                                                       0.0s
 => => transferring context: 84B                                                                                                                                                                                        0.0s
 => [deps 1/4] FROM docker.io/library/node:20-alpine@sha256:fb4cd12c85ee03686f6af5362a0b0d56d50c58a04632e6c0fb8363f609372293                                                                                            0.1s
 => => resolve docker.io/library/node:20-alpine@sha256:fb4cd12c85ee03686f6af5362a0b0d56d50c58a04632e6c0fb8363f609372293                                                                                                 0.0s
 => [internal] load build context                                                                                                                                                                                       0.0s
 => => transferring context: 7.32kB                                                                                                                                                                                     0.0s
 => CACHED [deps 2/4] WORKDIR /app                                                                                                                                                                                      0.0s
 => [deps 3/4] COPY package.json package-lock.json ./                                                                                                                                                                   0.1s
 => [deps 4/4] RUN npm ci --omit=dev                                                                                                                                                                                    2.6s
 => CACHED [runner 3/4] COPY --chown=node:node --from=deps /app/node_modules ./node_modules                                                                                                                             0.0s 
 => [runner 4/4] COPY --chown=node:node . .                                                                                                                                                                             0.0s
 => exporting to image                                                                                                                                                                                                  0.2s
 => => exporting layers                                                                                                                                                                                                 0.1s
 => => exporting manifest sha256:914b2fa2d79084c29dc435d7708ca1d2aaf972c140e043a9c2bbbb5e0e7a5bd4                                                                                                                       0.0s
 => => exporting config sha256:d26a243fc9a990764beee5ac7247454363441c9dc7f7c572dd1772c82500515d                                                                                                                         0.0s
 => => exporting attestation manifest sha256:25ef934fe43d133ca1de73722d3333bc39877285336afa0185f4b2b79203207c                                                                                                           0.0s
 => => exporting manifest list sha256:0dba35832bd770a3666d979e1346b1d72b8abc2665d747d27750e8e621d2e33a                                                                                                                  0.0s
 => => naming to docker.io/library/reservas-api:v1                                                                                                                                                                      0.0s
 => => unpacking to docker.io/library/reservas-api:v1      ---
 


 ```
 Punto 11: Publicar ambas imágenes en Docker Hub
# Iniciar sesión en Docker Hub
docker login

# Etiquetar ambas imágenes con el prefijo obligatorio de tu usuario
docker tag reservas-api:v1 TU_USUARIO/reservas-api:v1
docker tag reservas-frontend:v1 TU_USUARIO/reservas-frontend:v1

# Subir ambas imágenes al registry
docker push TU_USUARIO/reservas-api:v1
docker push TU_USUARIO/reservas-frontend:v1

The push refers to repository [docker.io/luisv3k/reservas-api]
226aaa3e4952: Pushed 
44136fa355b3: Already exists 
4feea04c1543: Layer already exists 
6a0ac1617861: Layer already exists 
b2cbbfe903b0: Layer already exists 
fff4e2c1b189: Layer already exists 
2fab92fb8dd9: Pushed 
86aab28e8529: Pushed 
565a85d1f3df: Pushed 
v1: digest: sha256:0dba35832bd770a3666d979e1346b1d72b8abc2665d747d27750e8e621d2e33a size: 856
estudiante@curso-docker:~/Documentos/reservas-app$


estudiante@curso-docker:~/Documentos/reservas-app$ docker push luisv3k/reservas-frontend:v1
The push refers to repository [docker.io/luisv3k/reservas-frontend]
576fe098e846: Layer already exists 
f18232174bc9: Layer already exists 
e11c47c4bbc7: Pushed 
44136fa355b3: Already exists 
945f5aaec3d7: Layer already exists 
513bae533eb2: Layer already exists 
8f7ead5f2dc0: Layer already exists 
048c29a61317: Layer already exists 
b16687ec2b7b: Layer already exists 
400c1de759f4: Layer already exists 
4fed3d46aa42: Pushed 
f79056b7935e: Layer already exists 
b36fbaee0102: Pushed 
v1: digest: sha256:7c12aee78d0162f927ca98721de506d539c21354e141ea512dabc1975eeec7d1 size: 856 ```



