# Docker — Fundamentos

## QUÉ es

Docker es una plataforma de **containerización** que empaqueta una aplicación con todas sus dependencias en un container aislado.

| Concepto | Definición | Analogía |
|----------|-----------|----------|
| **Dockerfile** | Receta para construir una imagen | Recipe de cocina |
| **Imagen** | Template inmutable | El molde de pastel |
| **Container** | Instancia corriendo de una imagen | El pastel horneado |
| **Registry** | Repositorio de imágenes | App Store de containers |

## POR QUÉ usar Docker

| Sin Docker | Con Docker |
|-----------|-----------|
| "En mi máquina funciona" | Funciona en todas partes |
| Instalar dependencias manualmente | Todo empaquetado en la imagen |
| Conflictos de versiones | Aislamiento perfecto |
| Deploy manual | Deploy consistente |

## CÓMO funciona

```
Dockerfile → docker build → Imagen → docker run → Container
                                                   (proceso corriendo)
```

## Comandos esenciales

```bash
# Construir imagen
docker build -t nombre:tag .

# Ejecutar container
docker run -p 8080:8080 nombre:tag

# Ver containers
docker ps              # Corriendo
docker ps -a           # Todos

# Logs
docker logs container-id
docker logs -f container-id  # Seguimiento en tiempo real

# Entrar al container
docker exec -it container-id /bin/bash

# Parar y eliminar
docker stop container-id
docker rm container-id
docker system prune -a  # Limpiar todo
```

## Multi-stage Build

```dockerfile
# Stage 1: Compilar
FROM mcr.microsoft.com/dotnet/sdk:10.0 AS build
WORKDIR /src
COPY . .
RUN dotnet publish -c Release -o /app

# Stage 2: Ejecutar (imagen pequeña)
FROM mcr.microsoft.com/dotnet/aspnet:10.0
WORKDIR /app
COPY --from=build /app .
ENTRYPOINT ["dotnet", "MyApp.dll"]
```

**Resultado:** Imagen de ~110MB en vez de ~800MB

## CUÁNDO usar

| Caso | ¿Docker? |
|------|---------|
| Desarrollo local | ✅ Sí |
| Testing | ✅ Sí |
| Producción | ✅ Sí |
| Microservicios | ✅ Sí |
| Monolito pequeño | ⚠️ Opcional |

## Referencia
- [[00-Index]]
- [[Docker/Dockerfile-Seguro]]
- [[Docker/Docker-Compose]]
