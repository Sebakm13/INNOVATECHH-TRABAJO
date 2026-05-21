# --- Etapa 1: Build/Instalación ---
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .

# --- Etapa 2: Ejecución ---
FROM node:20-alpine
WORKDIR /app
COPY package*.json ./
# Instalar solo dependencias de producción
RUN npm ci --only=production 
COPY --from=builder /app ./

# Crear usuario seguro sin privilegios (No-Root)
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
RUN chown -R appuser:appgroup /app
USER appuser

EXPOSE 3000
CMD ["node", "server.js"]
