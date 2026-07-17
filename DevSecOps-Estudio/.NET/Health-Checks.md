# .NET — Health Checks

## QUÉ son

Endpoints que **reportan el estado** de la aplicación y sus dependencias (BD, servicios externos, etc.).

## Tipos

| Tipo | Propósito | Kubernetes lo usa para |
|------|-----------|----------------------|
| **Liveness** | ¿El proceso está vivo? | `livenessProbe` — reiniciar si falla |
| **Readiness** | ¿Puede recibir tráfico? | `readinessProbe` — quitar del load balancer |
| **Startup** | ¿Terminó de iniciar? | `startupProbe` — esperar antes de los otros |

## CÓMO configurar en .NET

```bash
dotnet add package AspNetCore.HealthChecks.SqlServer
dotnet add package AspNetCore.HealthChecks.UI
```

```csharp
// Program.cs
builder.Services.AddHealthChecks()
    .AddSqlServer(connectionString, name: "sqlserver");

app.MapHealthChecks("/health");
app.MapHealthChecks("/health/ready", new HealthCheckOptions
{
    Predicate = check => check.Tags.Contains("ready")
});
app.MapHealthChecks("/health/live", new HealthCheckOptions
{
    Predicate = _ => false
});
```

## Respuesta

```json
// Healthy
{
  "status": "Healthy",
  "entries": {
    "sqlserver": { "status": "Healthy", "duration": "00:00:00.021" }
  }
}

// Unhealthy
{
  "status": "Unhealthy",
  "entries": {
    "sqlserver": { "status": "Unhealthy", "exception": "Connection refused" }
  }
}
```

## Referencia
- [[DevSecOps-Estudio/00-Index]]

#config #devsecops #database #dotnet #estudio #csharp #health-checks #dotnet
