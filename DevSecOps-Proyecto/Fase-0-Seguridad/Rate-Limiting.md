# Fase 0 — Rate Limiting

## ¿Qué es Rate Limiting?

Mecanismo que limita cuántas peticiones puede hacer un usuario en un período de tiempo dado.

### ¿Para qué sirve?
- Prevenir ataques de fuerza bruta
- Proteger contra DDoS
- Evitar abuso de la API
- Mantener la app estable bajo carga

## Estrategias de Rate Limiting

| Estrategia | Descripción | Ejemplo |
|-----------|-------------|---------|
| **Fixed Window** | X requests por ventana fija | 100 req/minuto |
| **Sliding Window** | Ventana deslizante | Promedio últimos 60s |
| **Token Bucket** | Tokens que se rellenan con tiempo | 10 tokens, 1/s |
| **Concurrent** | Límite de requests simultáneos | 10 a la vez |

## Configuración en .NET

```csharp
// Agregar rate limiting
builder.Services.AddRateLimiter(options =>
{
    options.RejectionStatusCode = StatusCodes.Status429TooManyRequests;

    options.AddFixedWindowLimiter("fixed", opts =>
    {
        opts.PermitLimit = 100;
        opts.Window = TimeSpan.FromMinutes(1);
        opts.QueueLimit = 10;
    });
});

// En el pipeline
app.UseRateLimiter();
```

### Aplicar a controllers específicos
```csharp
[ApiController]
[Route("api/[controller]")]
[EnableRateLimiting("fixed")]
public class AuthController : ControllerBase
{
    // Solo este controller tiene rate limiting
}
```

## Configuración recomendada por endpoint

| Endpoint | Límite | Razón |
|----------|--------|-------|
| `/login` | 5/min | Prevenir fuerza bruta |
| `/api/*` (lectura) | 100/min | Uso normal |
| `/api/*` (escritura) | 30/min | Prevenir abuso |
| `/health` | 1000/min | Health checks frecuentes |

## Respuesta 429

```json
{
  "type": "https://tools.ietf.org/html/rfc6585",
  "title": "Too Many Requests",
  "status": 429,
  "retryAfter": 30
}
```

## Notas personales
- 

## Links Relacionados
- [[00-Resumen-Fase-0]]
- [[JWT-Setup]]

## Referencia
- [[DevSecOps-Proyecto/00-Visión-General]]
- [[DevSecOps-Estudio/00-Index]]
- [[DevSecOps-Proyecto/Fase-0-Seguridad/Health-Checks]]
- [[DevSecOps-Proyecto/Fase-0-Seguridad/JWT-Setup]]
- [[DevSecOps-Proyecto/Fase-0-Seguridad/CORS-Config]]

#config #seguridad #devsecops #proyecto #api #rate-limiting #api #fase-0
