# Fase 0 — Health Checks

## ¿Qué son Health Checks?

Endpoints que reportan el estado de la aplicación y sus dependencias (BD, servicios externos, etc.).

### ¿Para qué sirven?
- Kubernetes sabe si el pod está vivo → auto-healing
- Load balancers saben a qué instancias enviar tráfico
- Monitoreo detecta problemas antes que los usuarios

## Tipos de Health Check

| Tipo | Propósito | Ejemplo |
|------|-----------|---------|
| **Liveness** | ¿El proceso está corriendo? | App responde HTTP 200 |
| **Readiness** | ¿Puede recibir tráfico? | BD conectada, dependencias OK |
| **Startup** | ¿Terminó de inicializar? | Migraciones aplicadas |

## Configuración en .NET (la que hicimos en SportsLeague)

Solo hizo falta un paquete (los de UI son opcionales, para el dashboard web):

```bash
dotnet add SportsLeague.API\SportsLeague.API.csproj package AspNetCore.HealthChecks.SqlServer
```

> `Microsoft.AspNetCore.Diagnostics.HealthChecks` ya viene incluido en ASP.NET Core. El paquete extra es el que enseña al check cómo probar SQL Server (manda un `SELECT 1`).

### 1. Registrar el servicio (junto a los servicios, después de AddControllers)

```csharp
// ── Health Checks ──
builder.Services.AddHealthChecks()
    .AddSqlServer(
        builder.Configuration.GetConnectionString("DefaultConnection")!,
        name: "sqlserver");
```

- `AddHealthChecks()` registra el servicio base en el DI.
- `.AddSqlServer(conn, name: "sqlserver")` agrega un check que ejecuta un `SELECT 1` contra la BD. El `name` sirve para identificar el check en los reportes.
- El `!` le dice al compilador "confío, no es null" (la connection string vive en User Secrets).

### 2. Crear los endpoints (después de UseAuthorization, antes de MapControllers)

```csharp
// ── Health Check Endpoints ──
app.MapHealthChecks("/health/live", new HealthCheckOptions
{
    Predicate = _ => false
});

app.MapHealthChecks("/health/ready", new HealthCheckOptions
{
    Predicate = _ => true
});
```

- `Predicate = _ => false` → en `/health/live` NO corre ningún check: solo responde si el proceso está vivo. No toca la BD.
- `Predicate = _ => true` → en `/health/ready` corre TODOS los checks (incluido SQL Server): si la BD está caída, responde 503.

Los endpoints quedan **públicos** (sin `[Authorize]`), porque un monitor/orquestador los consulta sin token.

## Prueba real que hicimos (SQL Server detenido)

| Endpoint | Con SQL corriendo | Con SQL detenido | HTTP |
|----------|-------------------|------------------|------|
| `/health/live` | Saludable | Saludable | 200 |
| `/health/ready` | Saludable | Sin salud | 503 |

`/health/live` nunca consulta la BD → sigue diciendo "saludable" aunque SQL esté apagado. `/health/ready` sí la prueba (`SELECT 1`) → al fallar, responde "sin salud" (503). **Esa es la gracia del tema**: el readiness avisa que la app no puede trabajar antes de que llegue tráfico que va a fallar.

## Respuesta de Health Check

### Healthy
```json
{
  "status": "Healthy",
  "totalDuration": "00:00:00.0234",
  "entries": {
    "sqlserver": {
      "status": "Healthy",
      "duration": "00:00:00.0212"
    }
  }
}
```

### Unhealthy
```json
{
  "status": "Unhealthy",
  "totalDuration": "00:00:00.0567",
  "entries": {
    "sqlserver": {
      "status": "Unhealthy",
      "duration": "00:00:00.0543",
      "exception": "Connection refused"
    }
  }
}
```

## Endpoints recomendados

| Endpoint | Propósito | Uso |
|----------|-----------|-----|
| `/health` | Todos los checks | Dashboard general |
| `/health/ready` | Checks de preparación | Kubernetes readinessProbe |
| `/health/live` | Solo liveness | Kubernetes livenessProbe |

## Notas personales
- En SportsLeague los endpoints se ven en `http://localhost:5277/health/live` y `/health/ready` (puerto definido en `launchSettings.json`, no inventado).
- Los 3 estados posibles son `Healthy` (200), `Degraded` (200, algo a medias) y `Unhealthy` (503). Con un solo check (SQL Server) solo veremos Healthy o Unhealthy.

## Errores comunes

| Error | Causa | Solución |
|-------|-------|----------|
| `Error de sistema 5. Acceso denegado` al detener SQL Server | Terminal sin permisos de administrador | Correr la terminal como admin o usar `services.msc` |
| `/health/ready` devuelve Unhealthy | SQL Server detenido o connection string inválido | Arrancar SQL Server / verificar User Secrets |
| `/health/live` y `/health/ready` devuelven lo mismo siempre | `Predicate` mal configurado o ambos con `_ => true` | El de live debe ser `_ => false` (no toca la BD) |

## Checklist

- [x] Paquete `AspNetCore.HealthChecks.SqlServer` instalado
- [x] `AddHealthChecks().AddSqlServer(...)` registrado en Program.cs
- [x] Endpoints `/health/live` (Predicate `false`) y `/health/ready` (Predicate `true`)
- [x] Probado con SQL Server detenido: live=saludable, ready=sin salud

## Mapeo OWASP Top 10:2025

| Categoría | Relación con Health Checks |
|---|---|
| A02 Security Misconfiguration | Endpoints de health expuestos sin protección pueden revelar info de la app si se loguean en exceso |
| A09 Security Logging and Alerting Failures | Health checks alimentan alertas tempranas de indisponibilidad (se conectan con monitoreo) |

## Links Relacionados
- [[00-Resumen-Fase-0]]
- [[Secrets-Management]]

## Referencia
- [[DevSecOps-Proyecto/00-Visión-General]]
- [[DevSecOps-Estudio/00-Index]]
- [[DevSecOps-Proyecto/Fase-0-Seguridad/Rate-Limiting]]
- [[DevSecOps-Proyecto/Fase-0-Seguridad/JWT-Setup]]
- [[DevSecOps-Proyecto/Fase-0-Seguridad/CORS-Config]]

#config #seguridad #devsecops #database #proyecto #monitoring #secrets #fase-0 #health-checks #dotnet
