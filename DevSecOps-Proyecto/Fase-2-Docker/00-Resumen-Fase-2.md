# Fase 2 — Docker Seguro

## Objetivo
Containerizar la aplicación con imágenes seguras, mínimas y escaneadas.

## Conceptos clave

| Concepto | Definición |
|----------|-----------|
| **Dockerfile** | Receta para construir una imagen Docker |
| **Imagen** | Template inmutable con todo lo necesario para correr la app |
| **Container** | Instancia corriendo de una imagen |
| **Multi-stage build** | Múltiples etapas para reducir tamaño de imagen final |
| **.dockerignore** | Archivos que NO se copian al container |

## Dockerfile Multi-Stage para .NET

```dockerfile
# ═══════════════════════════════════════════════════
# STAGE 1: Build
# ═══════════════════════════════════════════════════
FROM mcr.microsoft.com/dotnet/sdk:10.0 AS build
WORKDIR /src

# Copiar csproj y restaurar (caché de Docker)
COPY *.sln .
COPY TuProyecto.API/*.csproj TuProyecto.API/
COPY TuProyecto.Domain/*.csproj TuProyecto.Domain/
COPY TuProyecto.DataAccess/*.csproj TuProyecto.DataAccess/
RUN dotnet restore

# Copiar todo y compilar
COPY . .
RUN dotnet publish TuProyecto.API -c Release -o /app/publish --no-restore

# ═══════════════════════════════════════════════════
# STAGE 2: Runtime (imagen final)
# ═══════════════════════════════════════════════════
FROM mcr.microsoft.com/dotnet/aspnet:10.0 AS runtime

# SEGURIDAD: Usuario no-root
RUN addgroup --system appgroup && adduser --system --ingroup appgroup appuser

WORKDIR /app

# Copiar solo lo necesario del build stage
COPY --from=build /app/publish .

# SEGURIDAD: Cambiar ownership
RUN chown -R appuser:appgroup /app

# SEGURIDAD: Ejecutar como usuario no-root
USER appuser

EXPOSE 8080

ENTRYPOINT ["dotnet", "TuProyecto.API.dll"]
```

## .dockerignore

```gitignore
**/.git
**/.vs
**/.vscode
**/bin
**/obj
**/*.user
**/*.suo
**/docker-compose*.yml
**/Dockerfile*
**/.env
**/secrets.json
**/.gitignore
**/node_modules
**/coverage
```

## docker-compose.yml

```yaml
version: '3.8'

services:
  api:
    build:
      context: .
      dockerfile: TuProyecto.API/Dockerfile
    ports:
      - "5000:8080"
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ConnectionStrings__DefaultConnection=Server=sqlserver;Database=TuDB;User=sa;Password=YourStrong!Password123;TrustServerCertificate=true;
    depends_on:
      sqlserver:
        condition: service_healthy
    networks:
      - app-network

  sqlserver:
    image: mcr.microsoft.com/mssql/server:2022-latest
    environment:
      - ACCEPT_EULA=Y
      - SA_PASSWORD=YourStrong!Password123
    ports:
      - "1433:1433"
    volumes:
      - sqlserver-data:/var/opt/mssql
    healthcheck:
      test: /opt/mssql-tools*/bin/sqlcmd -S localhost -U sa -P "YourStrong!Password123" -Q "SELECT 1" || exit 1
      interval: 10s
      timeout: 5s
      retries: 5
    networks:
      - app-network

volumes:
  sqlserver-data:

networks:
  app-network:
    driver: bridge
```

## SEGURIDAD en Docker

### Principios

| Principio | Implementación |
|-----------|---------------|
| Imagen mínima | Multi-stage build, solo runtime |
| Non-root user | `USER appuser` en Dockerfile |
| No secrets en imagen | Variables de entorno o Docker secrets |
| Read-only filesystem | `--read-only` en docker run |
| No capabilities | `--cap-drop ALL` |

### Comandos de seguridad
```bash
# Ejecutar con opciones de seguridad
docker run --read-only --cap-drop ALL -p 5000:8080 tu-imagen

# Escanear imagen con Trivy
trivy image tu-imagen:latest

# Escanear Dockerfile
trivy config Dockerfile
```

## Trivy (Escaneo de vulnerabilidades)

```bash
# Instalar Trivy
# Windows: choco install trivy
# Mac: brew install trivy
# Linux: apt install trivy

# Escanear imagen
trivy image tu-imagen:latest

# Escanear solo vulnerabilities Critical/High
trivy image --severity CRITICAL,HIGH tu-imagen:latest

# Escanear como JSON
trivy image -f json -o results.json tu-imagen:latest

# Escanear Dockerfile (IaC scanning)
trivy config --severity CRITICAL,HIGH .
```

## Comandos Docker útiles
```bash
# Build
docker build -t tu-imagen:latest .

# Ejecutar
docker run -p 5000:8080 tu-imagen:latest

# Ver containers corriendo
docker ps

# Ver logs
docker logs <container-id>

# Entrar al container
docker exec -it <container-id> /bin/bash

# Limpiar imágenes no usadas
docker system prune -a
```

## Notas personales
- 

## Links Relacionados
- [[00-Resumen-Fase-0]]
- [[Glosario-DevSecOps]]
