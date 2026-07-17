# Fase 6 — Monitoreo y Observabilidad

## Objetivo
Tener visibilidad completa de la aplicación: qué está pasando, qué errores ocurren, y cuándo algo va mal.

## Los 3 pilares de la Observabilidad

| Pilar | Pregunta | Herramienta |
|-------|----------|------------|
| **Logs** | ¿Qué pasó? | Serilog + ELK/Loki |
| **Metrics** | ¿Qué tan bien funciona? | Prometheus |
| **Traces** | ¿Dónde está el problema? | Jaeger/Zipkin |

## Structured Logging con Serilog

### ¿Por qué structured logging?
Los logs tradicionales son texto plano difícil de buscar. Structured logging genera JSON, fácil de parsear y buscar.

```
TRADICIONAL:
2026-07-14 12:00:00 ERROR Failed to connect to database

STRUCTURED:
{"timestamp":"2026-07-14T12:00:00Z","level":"Error","message":"Failed to connect","sourceContext":"LeagueDbContext","actionId":"abc123"}
```

### Configuración
```bash
dotnet add package Serilog.AspNetCore
dotnet add package Serilog.Sinks.Console
dotnet add package Serilog.Sinks.File
```

```csharp
// Program.cs
builder.Host.UseSerilog((context, config) =>
{
    config
        .ReadFrom.Configuration(context.Configuration)
        .Enrich.FromLogContext()
        .Enrich.WithMachineName()
        .Enrich.WithEnvironmentName()
        .WriteTo.Console(outputTemplate:
            "[{Timestamp:HH:mm:ss} {Level:u3}] {Message:lj}{NewLine}{Exception}");
});
```

### appsettings.json
```json
{
  "Serilog": {
    "MinimumLevel": {
      "Default": "Information",
      "Override": {
        "Microsoft": "Warning",
        "System": "Warning"
      }
    },
    "WriteTo": [
      {
        "Name": "File",
        "Args": {
          "path": "logs/log-.txt",
          "rollingInterval": "Day",
          "retainedFileCountLimit": 30
        }
      }
    ]
  }
}
```

## Prometheus (Metrics)

### Métricas importantes para tu API

| Métrica | Tipo | Descripción |
|---------|------|-------------|
| `http_requests_total` | Counter | Total de requests |
| `http_request_duration_seconds` | Histogram | Duración de requests |
| `http_requests_in_flight` | Gauge | Requests en proceso |
| `dotnet_exceptions_total` | Counter | Total de excepciones |

### Configuración
```bash
dotnet add package prometheus-net.AspNetCore
```

```csharp
// Program.cs
app.UseHttpMetrics();
app.MapMetrics("/metrics");
```

## Grafana (Dashboards)

### Setup con Docker Compose
```yaml
  prometheus:
    image: prom/prometheus
    ports:
      - "9090:9090"
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml

  grafana:
    image: grafana/grafana
    ports:
      - "3000:3000"
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=admin
```

### Dashboard de seguridad a monitorear

| Panel | Métrica | Alerta |
|-------|---------|--------|
| Requests por status code | `http_requests_total` | > 10% errores 5xx |
| Latencia p99 | `http_request_duration_seconds` | > 2 segundos |
| Tasa de errores | `http_requests_total{status=~"5.."}` | > 5% |
| Autenticación fallida | Logs con "Invalid credentials" | > 10/minuto |
| Rate limit hits | Logs con "429" | > 50/minuto |

## Alertas de Seguridad

```yaml
# rules.yml para Prometheus
groups:
  - name: security-alerts
    rules:
      - alert: HighErrorRate
        expr: rate(http_requests_total{status=~"5.."}[5m]) > 0.05
        for: 2m
        labels:
          severity: critical
        annotations:
          summary: "Tasa de errores alta"

      - alert: HighLatency
        expr: histogram_quantile(0.99, rate(http_request_duration_seconds_bucket[5m])) > 2
        for: 2m
        labels:
          severity: warning
        annotations:
          summary: "Latencia p99 alta"

      - alert: BruteForceDetected
        expr: rate(http_requests_total{path="/api/auth/login",status="401"}[1m]) > 0.1
        for: 1m
        labels:
          severity: critical
        annotations:
          summary: "Posible ataque de fuerza bruta"
```

## Security Logging

### Qué loguear por seguridad

| Evento | Nivel | Datos a incluir |
|--------|-------|----------------|
| Login exitoso | Information | userId, IP, timestamp |
| Login fallido | Warning | email, IP, reason |
| Rate limit hit | Warning | IP, endpoint, count |
| Autorización denegada | Warning | userId, endpoint, role |
| Excepción | Error | stack trace, context |
| Request recibido | Debug | method, path, headers |

### Ejemplo de security logging
```csharp
[HttpPost("login")]
public async Task<IActionResult> Login(LoginDTO dto)
{
    try
    {
        var user = await _authService.Authenticate(dto.Email, dto.Password);
        _logger.LogInformation("Login exitoso para {Email} desde {IP}",
            dto.Email, HttpContext.Connection.RemoteIpAddress);
        return Ok(new { token = GenerateJwt(user) });
    }
    catch (UnauthorizedAccessException)
    {
        _logger.LogWarning("Login fallido para {Email} desde {IP}",
            dto.Email, HttpContext.Connection.RemoteIpAddress);
        return Unauthorized();
    }
}
```

## Checklist de Monitoreo
- [ ] Serilog configurado con structured logging
- [ ] Logs a archivo con rolling diario
- [ ] Prometheus configurado con métricas HTTP
- [ ] Grafana con dashboard básico
- [ ] Alertas de error rate
- [ ] Alertas de latencia
- [ ] Security logging (login fallidos, rate limits)

## Notas personales
- 

## Links Relacionados
- [[00-Resumen-Fase-0]]
- [[DevSecOps-Proyecto/Referencia/Glosario-DevSecOps]]

## Referencia
- [[DevSecOps-Proyecto/00-Visión-General]]
- [[DevSecOps-Estudio/00-Index]]

#debug #config #devsecops #database #proyecto #api #logging #fase-6 #monitoreo #monitoring #checklist
