# Autenticación y Autorización

> [!info] OWASP Top 10:2025
> **A07:Authentication Failures** — Se mantiene en #7. Incluye autenticación débil, manejo de sesiones, y falta de MFA.

## QUÉ es

| Concepto | Definición | Pregunta | Ejemplo |
|----------|-----------|----------|---------|
| **Autenticación** | Verificar QUIÉN eres | "¿Quién eres?" | Login con email + password |
| **Autorización** | Verificar QUÉ puedes hacer | "¿Qué puedes hacer?" | Admin puede borrar, User solo lee |

> **Clave:** Son dos controles distintos. Autenticarse no significa tener acceso a todo. Un usuario puede ser autenticado (sabemos quién es) pero no autorizado (no tiene permiso para esa acción).

## POR QUÉ importa

| Sin autenticación | Sin autorización |
|-------------------|------------------|
| Cualquiera accede a tu API | Un usuario normal puede borrar todo |
| No puedes rastrear quién hizo qué | No hay separación de roles |
| No puedes controlar permisos | Un atacante con token válido accede a todo |

## JWT — Cómo funciona

```
1. Usuario envía credenciales (email + password)
         │
         ▼
2. Servidor valida → genera JWT con claims (userId, role, exp)
         │
         ▼
3. JWT viaja al cliente
         │
         ▼
4. Cliente envía JWT en header: Authorization: Bearer <token>
         │
         ▼
5. Servidor valida firma y claims en cada request
```

### Estructura de un JWT

```
Header.Payload.Signature

eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwicm9sZSI6IkFkbWluIn0.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

| Parte | Contenido |
|-------|-----------|
| **Header** | Algoritmo de firma (HS256, RS256) y tipo de token |
| **Payload** | Claims (userId, role, exp, iat) |
| **Signature** | Firma criptográfica del header + payload con la secret key |

> [!warning] El payload NO está encriptado
> JWT solo está **firmado**, no cifrado. Cualquiera puede decodificar el payload (jwt.io). Nunca guardes información sensible (passwords, datos personales) en el payload.

### Claims comunes

| Claim | Significado | Tipo |
|-------|------------|------|
| `sub` | Subject — ID del usuario | Estándar |
| `iat` | Issued At — cuándo se emitió | Estándar |
| `exp` | Expiration — cuándo expira | Estándar |
| `iss` | Issuer — quién lo emitió | Estándar |
| `aud` | Audience — para quién es válido | Estándar |
| `role` | Rol del usuario (Admin, User...) | Custom |

## Cuándo usar cada mecanismo

| Mecanismo | Cuándo usar | Cuándo NO usar |
|-----------|-------------|----------------|
| **JWT** | APIs stateless, microservicios | Si necesitas revocar tokens de inmediato |
| **Session Cookies** | Apps web tradicionales | APIs móviles |
| **API Keys** | Servicio a servicio | Autenticación de usuarios finales |
| **OAuth 2.0** | Login con Google/GitHub | Si controlas todos los usuarios directamente |
| **mTLS** | Microservicios internos | Apps públicas |

## CÓMO configurar en .NET

```csharp
// Program.cs — Registro de autenticación
builder.Services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(options =>
    {
        options.TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = true,
            ValidateAudience = true,
            ValidateLifetime = true,
            ValidateIssuerSigningKey = true,
            ValidIssuer = builder.Configuration["Jwt:Issuer"],
            ValidAudience = builder.Configuration["Jwt:Audience"],
            IssuerSigningKey = new SymmetricSecurityKey(
                Encoding.UTF8.GetBytes(builder.Configuration["Jwt:Key"]))
        };
    });

var app = builder.Build();

// ORDEN CRÍTICO: Authentication ANTES de Authorization
app.UseAuthentication();
app.UseAuthorization();
```

### Autorización por rol

```csharp
// Proteger un controller completo
[ApiController]
[Route("api/[controller]")]
[Authorize(Roles = "Admin")]
public class AdminController : ControllerBase { ... }

// Proteger una acción específica
[Authorize(Roles = "Admin")]
[HttpDelete("{id}")]
public IActionResult Delete(int id) { ... }

// Solo usuarios autenticados (cualquier rol)
[Authorize]
[HttpGet]
public IActionResult GetAll() { ... }
```

## Refresh Tokens — patrón recomendado

| Token | Vida | Uso | Almacenamiento |
|-------|------|-----|----------------|
| **Access token** | 5-15 min | Cada request | Memory del cliente |
| **Refresh token** | Días/semanas | Obtener nuevo access token | httpOnly cookie o storage seguro |

> **Buenas prácticas:**
> - **Rotar** refresh tokens (invalidar el anterior al usar uno nuevo)
> - Guardar refresh tokens en BD o Redis para poder revocarlos
> - Detectar uso de refresh token desde otra IP → invalidar sesión

## Errores comunes

| Error | Causa | Solución |
|-------|-------|----------|
| `401 Unauthorized` | Token no enviado o inválido | Verificar header `Authorization: Bearer <token>` |
| `403 Forbidden` | Token válido pero sin permisos | Verificar roles/claims |
| `SecurityTokenExpiredException` | Token expirado | Implementar refresh tokens |
| `InvalidIssuerSigningKey` | Secret key incorrecta | Verificar configuración |

## Tips de seguridad

1. **Nunca** guardes passwords en texto plano — usa bcrypt/argon2
2. **Siempre** valida la firma y expiración del token en cada request
3. Usa HTTPS — nunca envíes tokens por HTTP plano
4. Implementa refresh tokens con rotación para sesiones largas
5. Rate limit en el endpoint de login
6. No guardes datos sensibles en el payload (no está cifrado)
7. Usa algoritmos asimétricos (RS256) si varios servicios necesitan **verificar** pero no **emitir** tokens
8. Considera MFA (Multi-Factor Authentication) para cuentas sensibles

## Referencia
- [[DevSecOps-Estudio/00-Index]]
- [[Patrones/Anti-Patrones-Seguridad]]
- [[DevSecOps-Proyecto/Fase-0-Seguridad/JWT-Setup]]

---
#seguridad #autenticacion #jwt #oauth #owasp #authorization
