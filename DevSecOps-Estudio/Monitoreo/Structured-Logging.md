# Structured Logging con Serilog

## QUÉ es

Formato de logs donde cada línea es un **objeto JSON** en vez de texto plano, facilitando búsqueda, filtrado y análisis.

## POR QUÉ usar structured logging

```
TRADICIONAL (texto plano):
2026-07-14 12:00:00 ERROR Failed to connect to database for user admin from 192.168.1.1

  ❌ Difícil de buscar
  ❌ Difícil de parsear
  ❌ Difícil de alertar

STRUCTURED (JSON):
{
  "timestamp": "2026-07-14T12:00:00Z",
  "level": "Error",
  "message": "Failed to connect to database",
  "user": "admin",
  "ip": "192.168.1.1",
  "source": "LeagueDbContext"
}

  ✅ Búsqueda fácil
  ✅ Parse automático
  ✅ Alertas precisas
```

## CÓMO configurar en .NET

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
        .WriteTo.Console();
});
```

## Qué loguear

| Evento | Nivel | Datos |
|--------|-------|-------|
| Request recibido | Debug | method, path |
| Login exitoso | Information | userId, IP |
| Login fallido | Warning | email, IP, reason |
| Rate limit hit | Warning | IP, endpoint |
| Autorización denegada | Warning | userId, endpoint |
| Excepción | Error | stack trace |
| Crash | Fatal | todo el contexto |

## Niveles de log

| Nivel | Cuándo usar |
|-------|-------------|
| `Verbose` / `Debug` | Información detallada para debugging |
| `Information` | Eventos normales del sistema |
| `Warning` | Algo inesperado pero no crítico |
| `Error` | Error que necesita atención |
| `Fatal` | Error que tumba la aplicación |

## Referencia
- [[DevSecOps-Estudio/00-Index]]
- [[Monitoreo/Prometheus]]
- [[Monitoreo/Grafana]]
