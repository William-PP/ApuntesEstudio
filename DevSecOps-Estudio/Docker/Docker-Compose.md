# Docker Compose

## QUÉ es

Herramienta para **orquestar múltiples containers** con un solo archivo YAML.

## POR QUÉ usarlo

| Sin Compose | Con Compose |
|-------------|-------------|
| `docker run` con 10 flags | `docker-compose up` |
| Containers sueltos | Servicios relacionados |
| Config manual de redes | Redes automáticas |
| Sin volumes persistentes | Volumes configurados |

## CÓMO funciona

```yaml
# docker-compose.yml
version: '3.8'

services:
  api:
    build: .
    ports:
      - "5000:8080"
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
    depends_on:
      sqlserver:
        condition: service_healthy

  sqlserver:
    image: mcr.microsoft.com/mssql/server:2022-latest
    environment:
      - ACCEPT_EULA=Y
      - SA_PASSWORD=YourStrong!Password123
    volumes:
      - sqlserver-data:/var/opt/mssql
    healthcheck:
      test: /opt/mssql-tools*/bin/sqlcmd -S localhost -U sa -P "YourStrong!Password123" -Q "SELECT 1"
      interval: 10s
      timeout: 5s
      retries: 5

volumes:
  sqlserver-data:
```

## Comandos

```bash
# Iniciar todo
docker-compose up -d

# Ver logs
docker-compose logs -f

# Parar todo
docker-compose down

# Reconstruir
docker-compose up --build

# Ver servicios
docker-compose ps
```

## CUÁNDO usar

- Desarrollo local con BD
- Testing de integración
- Ambientes de staging
- NO para producción (usar K8s)

## Referencia
- [[00-Index]]
- [[Docker/Fundamentos]]
