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


