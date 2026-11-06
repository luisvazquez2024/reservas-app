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
