# --- Etapa 1: Construcción (Build) ---
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install --legacy-peer-deps
COPY . .
RUN npm run build
# --- Etapa 2: Producción ---
FROM nginx:alpine
# Copiar los archivos construidos a Nginx
COPY --from=builder /app/dist /usr/share/nginx/html
# Configurar usuario no-root para Nginx (Buenas prácticas)
RUN touch /var/run/nginx.pid && \
    chown -R nginx:nginx /var/run/nginx.pid /var/cache/nginx /var/log/nginx /usr/share/nginx/html
USER nginx
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
