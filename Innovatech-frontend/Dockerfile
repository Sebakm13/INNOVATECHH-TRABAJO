# Etapa 1: Build de React
FROM node:18-alpine AS build
WORKDIR /app
COPY front_despacho/package*.json ./
RUN npm install
COPY front_despacho/ .
RUN npm run build

# Etapa 2: Servidor de producción seguro (Nginx)
FROM nginx:alpine
RUN rm -rf /etc/nginx/conf.d/* /etc/nginx/nginx.conf
COPY nginx.conf /etc/nginx/nginx.conf
COPY --from=build /app/dist /usr/share/nginx/html
RUN touch /var/run/nginx.pid && \
    chown -R nginx:nginx /var/run/nginx.pid /var/cache/nginx /var/log/nginx /etc/nginx
USER nginx
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
