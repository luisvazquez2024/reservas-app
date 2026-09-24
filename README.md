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