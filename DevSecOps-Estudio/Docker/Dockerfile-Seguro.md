# Dockerfile Seguro

## QUÉ es

Un Dockerfile con configuraciones de seguridad que reducen la superficie de ataque de la imagen.

## Principios de seguridad

| Principio | Implementación | Impacto |
|-----------|---------------|---------|
| Imagen mínima | Multi-stage build | Menos vulnerabilidades |
| Non-root user | `USER appuser` | No puede hacer cambios al sistema |
| No secrets | Variables de entorno | Secrets no en la imagen |
| Filesystem read-only | `--read-only` en runtime | No puede modificar archivos |
| Sin capabilities | `--cap-drop ALL` | Sin permisos del kernel |

## Template seguro para .NET

```dockerfile
# ═══════════════════════════════════════════════════
# STAGE 1: Build
# ═══════════════════════════════════════════════════
FROM mcr.microsoft.com/dotnet/sdk:10.0 AS build
WORKDIR /src

COPY *.sln .
COPY TuProyecto.API/*.csproj TuProyecto.API/
COPY TuProyecto.Domain/*.csproj TuProyecto.Domain/
COPY TuProyecto.DataAccess/*.csproj TuProyecto.DataAccess/
RUN dotnet restore

COPY . .
RUN dotnet publish TuProyecto.API -c Release -o /app/publish --no-restore

# ═══════════════════════════════════════════════════
# STAGE 2: Runtime (SEGURA)
# ═══════════════════════════════════════════════════
FROM mcr.microsoft.com/dotnet/aspnet:10.0 AS runtime

# SEGURIDAD: Crear usuario no-root
RUN addgroup --system appgroup && adduser --system --ingroup appgroup appuser

# SEGURIDAD: No instalar herramientas innecesarias
RUN rm -rf /var/lib/apt/lists/*

WORKDIR /app
COPY --from=build /app/publish .

# SEGURIDAD: Cambiar ownership
RUN chown -R appuser:appgroup /app

# SEGURIDAD: Ejecutar como no-root
USER appuser

EXPOSE 8080
ENTRYPOINT ["dotnet", "TuProyecto.API.dll"]
```

## .dockerignore

```gitignore
**/.git
**/.vs
**/bin
**/obj
**/*.user
**/*.suo
**/docker-compose*.yml
**/Dockerfile*
**/.env
**/secrets.json
**/.gitignore
**/coverage
**/test-results
```

## Errores comunes

| Error | Causa | Solución |
|-------|-------|---------|
| `Permission denied` | Container corriendo como root pero archivo es de otro user | `RUN chown -R appuser:appgroup /app` |
| Imagen muy grande | No usa multi-stage | Agregar stage de build + runtime |
| Secrets en imagen | Hardcoded en Dockerfile | Usar env vars o secrets |

## Referencia
- [[00-Index]]
- [[Docker/Fundamentos]]
- [[Docker/Trivy]]
