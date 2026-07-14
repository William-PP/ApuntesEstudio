# Prometheus — Metrics

## QUÉ es

Sistema de **monitoreo y recolección de métricas** que usa un modelo pull (va a buscar las métricas a tu app).

## Métricas importantes para APIs

| Métrica | Tipo | Descripción |
|---------|------|-------------|
| `http_requests_total` | Counter | Total de requests |
| `http_request_duration_seconds` | Histogram | Duración de requests |
| `http_requests_in_flight` | Gauge | Requests en proceso |
| `http_requests_total{status="500"}` | Counter | Errores del servidor |

## CÓMO configurar en .NET

```bash
dotnet add package prometheus-net.AspNetCore
```

```csharp
// Program.cs
app.UseHttpMetrics();
app.MapMetrics("/metrics");
```

## CUÁNDO alertar

| Condición | Alerta | Severidad |
|-----------|--------|-----------|
| Error rate > 5% por 2 min | 🔴 Crítica | Alto |
| Latencia p99 > 2s | 🟡 Warning | Medio |
| Pod restarts > 3 | 🔴 Crítica | Alto |
| Memory > 80% | 🟡 Warning | Medio |

## Referencia
- [[00-Index]]
- [[Monitoreo/Structured-Logging]]
- [[Monitoreo/Grafana]]
