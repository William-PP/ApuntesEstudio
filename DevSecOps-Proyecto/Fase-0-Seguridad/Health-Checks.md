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

## Configuración en .NET

```bash
dotnet add package AspNetCore.HealthChecks.SqlServer
dotnet add package AspNetCore.HealthChecks.UI
dotnet add package AspNetCore.HealthChecks.UI.Client
```

```csharp
// En Program.cs
builder.Services.AddHealthChecks()
    .AddSqlServer(
        connectionString: builder.Configuration.GetConnectionString("DefaultConnection"),
        name: "sqlserver",
        tags: new[] { "db", "ready" }
    );

// Endpoints
app.MapHealthChecks("/health", new HealthCheckOptions
{
    Predicate = _ => true,
    ResponseWriter = UIResponseWriter.WriteHealthCheckUIResponse
});

app.MapHealthChecks("/health/ready", new HealthCheckOptions
{
    Predicate = check => check.Tags.Contains("ready")
});

app.MapHealthChecks("/health/live", new HealthCheckOptions
{
    Predicate = _ => false  // Solo verifica que el proceso viva
});
```

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
- 

## Links Relacionados
- [[00-Resumen-Fase-0]]
- [[Secrets-Management]]
