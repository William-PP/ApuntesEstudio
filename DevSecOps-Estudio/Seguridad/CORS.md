# CORS (Cross-Origin Resource Sharing)

> [!info] OWASP Top 10:2025
> **A02:Security Misconfiguration** — CORS mal configurado cae en esta categoría. Subió de #5 a #2 en 2025.

## QUÉ es

Mecanismo de seguridad del navegador que **controla qué dominios pueden acceder** a los recursos de tu API. Sin CORS, cualquier sitio web podría hacer requests a tu API usando las credenciales del usuario.

```
Tu app web (banco.com) → API (banco.com/api)  ✅ Permitido (mismo origen)
Sitio malicioso (evil.com) → API (banco.com/api)  ❌ Bloqueado por CORS
```

## POR QUÉ existe

| Sin CORS | Con CORS |
|----------|----------|
| Cualquier sitio puede hacer peticiones a tu API | Solo dominios autorizados pueden acceder |
| Atacante puede usar la sesión del usuario | Navegador bloquea requests no autorizados |
| Robo de datos vía JavaScript malicioso | Control granular de orígenes |

## CÓMO funciona el flujo

```
1. Navegador envía preflight (OPTIONS) a la API
         │
         ▼
2. API responde con headers CORS:
   Access-Control-Allow-Origin: https://tudominio.com
   Access-Control-Allow-Methods: GET, POST, PUT, DELETE
   Access-Control-Allow-Headers: Content-Type, Authorization
         │
         ▼
3. Si está permitido → navegador envía el request real
   Si NO está permitido → navegador bloquea la respuesta
```

> **Preflight:** Para requests "no simples" (POST con JSON body, headers custom, etc.), el navegador envía primero un request OPTIONS. Si la API no responde correctamente, el request real nunca se envía.

## CUÁNDO configurarlo

- **SIEMPRE** que tu frontend y backend estén en dominios diferentes (incluso puertos diferentes)
- Apps SPA (React/Angular/Vue) consumiendo API
- Microservicios comunicándose entre sí

| Escenario | ¿Necesita CORS? |
|-----------|----------------|
| Frontend `localhost:3000` → API `localhost:5000` | ✅ Sí (puertos diferentes = orígenes diferentes) |
| Misma URL exacta (same-origin) | ❌ No |
| App móvil → API | ❌ No (no tiene navegador) |
| Microservicio → microservicio | ❌ No (server-to-server) |

## CÓMO configurar en .NET

```csharp
// Registrar política de CORS
builder.Services.AddCors(options =>
{
    options.AddPolicy("AllowFrontend", policy =>
    {
        policy.WithOrigins(
                "http://localhost:3000",    // Desarrollo
                "https://tudominio.com"     // Producción
              )
              .AllowAnyHeader()
              .AllowAnyMethod()
              .AllowCredentials();  // Solo si usas cookies/auth
    });
});

// En el pipeline — ANTES de Authorization
app.UseCors("AllowFrontend");
app.UseAuthentication();
app.UseAuthorization();
```

## DÓNDE NO hacer

```csharp
// ❌ INSEGURO — permitir todos los orígenes
policy.AllowAnyOrigin()

// ❌ INSEGURO — en producción con credenciales
policy.AllowAnyOrigin().AllowCredentials()

// ❌ INSEGURO — usar null o wildcard
policy.SetIsOriginAllowed(_ => true)

// ✅ BIEN — origen específico
policy.WithOrigins("https://tudominio.com")
      .AllowAnyHeader()
      .AllowAnyMethod();
```

## Headers importantes

| Header | Quién lo pone | Ejemplo |
|--------|--------------|---------|
| `Access-Control-Allow-Origin` | Servidor | `https://tudominio.com` |
| `Access-Control-Allow-Methods` | Servidor | `GET, POST, PUT, DELETE` |
| `Access-Control-Allow-Headers` | Servidor | `Content-Type, Authorization` |
| `Access-Control-Allow-Credentials` | Servidor | `true` |
| `Access-Control-Max-Age` | Servidor | `3600` (cache preflight en segundos) |
| `Origin` | Navegador | `https://tudominio.com` |

## Errores comunes

| Error en consola del navegador | Causa | Solución |
|-------------------------------|-------|----------|
| `No 'Access-Control-Allow-Origin' header` | Origen no está en la lista | Agregar origin a `WithOrigins()` |
| `Method not allowed` | Método HTTP no permitido | Agregar `.AllowAnyMethod()` o método específico |
| `Credential is not supported` | `AllowCredentials` sin origen específico | No se puede usar `AllowAnyOrigin()` con credenciales |

## Referencia
- [[DevSecOps-Estudio/00-Index]]
- [[Patrones/Anti-Patrones-Seguridad]]
- [[DevSecOps-Proyecto/Fase-0-Seguridad/CORS-Config]]

---
#seguridad #cors #owasp #misconfiguration #api
