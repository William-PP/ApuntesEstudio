# Rate Limiting

> [!info] OWASP Top 10:2025
> **A01:Broken Access Control** y **A07:Authentication Failures** — Rate limiting protege contra fuerza bruta (A07) y abuso de endpoints (A01).

## QUÉ es

Mecanismo que **limita cuántas peticiones** puede hacer un usuario en un período de tiempo. Es una capa de defensa contra abuso y ataques.

## POR QUÉ importa

| Amenaza | Sin Rate Limiting | Con Rate Limiting |
|---------|-------------------|-------------------|
| Fuerza bruta | Miles de intentos de login por segundo | 5 intentos/minuto |
| DDoS | Servidor saturado | Requests rechazados con 429 |
| Scraping | Todo el contenido copiado | Límite de requests por IP |
| Abuso de API | Costos elevados en cloud | Control de uso |

## Estrategias

| Estrategia | Cómo funciona | Cuándo usar | Ventaja |
|-----------|---------------|-------------|---------|
| **Fixed Window** | X requests por ventana fija (ej: 100/min) | Uso general | Simple de implementar |
| **Sliding Window** | Ventana deslizante (promedio últimos 60s) | Más preciso que Fixed | Evita picos en bordes de ventana |
| **Token Bucket** | Tokens que se rellenan con tiempo | Control de tasa constante | Permite ráfagas controladas |
| **Concurrent** | Límite de requests simultáneos | Protección de recursos | Controla concurrencia real |

### Fixed Window vs Sliding Window

```
FIXED WINDOW:
|__________|__________|__________
   min 0       min 1       min 2
   [100 req]   [100 req]
   
Problema: Si haces 100 req a las 00:59 y 100 a las 01:00,
son 200 req en 2 minutos, pero 100 en 1 minuto real.

SLIDING WINDOW:
     |__________|__________
     ↕ deslizante
   Promedio de los últimos 60 segundos
   Más preciso, evita el problema del borde
```

## CÓMO configurar en .NET

```csharp
builder.Services.AddRateLimiter(options =>
{
    options.RejectionStatusCode = StatusCodes.Status429TooManyRequests;

    // Fixed Window para uso general
    options.AddFixedWindowLimiter("general", opts =>
    {
        opts.PermitLimit = 100;
        opts.Window = TimeSpan.FromMinutes(1);
    });

    // Fixed Window más estricto para auth
    options.AddFixedWindowLimiter("auth", opts =>
    {
        opts.PermitLimit = 5;
        opts.Window = TimeSpan.FromMinutes(1);
    });
});

// En el pipeline
app.UseRateLimiter();
```

### Aplicar por endpoint

```csharp
[HttpPost("login")]
[EnableRateLimiting("auth")]
public async Task<IActionResult> Login(LoginDTO dto) { ... }

[HttpGet]
[EnableRateLimiting("general")]
public IActionResult GetAll() { ... }
```

## Límites recomendados por endpoint

| Endpoint | Límite | Razón |
|----------|--------|-------|
| `/login` | 5/min | Prevenir fuerza bruta |
| `/register` | 3/hora | Prevenir spam |
| `/api/*` (lectura) | 100/min | Uso normal |
| `/api/*` (escritura) | 30/min | Prevenir abuso |
| `/health` | 1000/min | Health checks frecuentes |
| `/api/upload` | 10/min | Prevenir abuso de recursos |

## CUÁNDO usar

- **SIEMPRE** en endpoints de autenticación
- En APIs públicas
- En endpoints que consumen recursos (archivos, reportes)
- En cualquier lugar donde un abuso pueda causar daño

## DÓNDE implementar

```
Request → Rate Limiter → Controller → Response
                    │
                    └── Si excede límite → 429 Too Many Requests
```

## Respuesta 429

```json
{
  "type": "https://tools.ietf.org/html/rfc6585",
  "title": "Too Many Requests",
  "status": 429,
  "retryAfter": 30
}
```

> **Buenas prácticas:**
> - Incluir header `Retry-After` para que el cliente sepa cuándo reintentar
> - Loguear rate limit hits para detectar patrones de ataque
> - Considerar rate limiting por API key en addition a por IP

## Errores comunes

| Error | Causa | Solución |
|-------|-------|----------|
| `429 Too Many Requests` (esperado) | Usuario excedió el límite | Implementar retry logic con backoff |
| Rate limit demasiado estricto | Límites muy bajos para uso normal | Ajustar según tráfico real |
| Rate limit solo por IP | Usuarios detrás de NAT comparten IP | Considerar rate limit por API key o token |

## Referencia
- [[DevSecOps-Estudio/00-Index]]
- [[Patrones/Anti-Patrones-Seguridad]]
- [[DevSecOps-Proyecto/Fase-0-Seguridad/Rate-Limiting]]

---
#seguridad #rate-limiting #owasp #ddos #brute-force
