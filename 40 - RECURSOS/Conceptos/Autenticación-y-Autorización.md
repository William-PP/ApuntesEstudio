---
type: concepto
state: activa
created: 2026-08-15
updated: 2026-08-23
tags: [seguridad, autenticacion, jwt, oauth, owasp, authorization, mfa, sesiones, fuerza-bruta]
aliases: [Autenticación, Autorización]
---

# Autenticación y Autorización

> [!info] OWASP Top 10:2025
> **A07:Authentication Failures** — Se mantiene en #7. Incluye autenticación débil, manejo de sesiones y falta de MFA.

## QUÉ es

| Concepto | Definición | Pregunta | Ejemplo |
|----------|-----------|----------|---------|
| **Autenticación** | Verificar QUIÉN eres | "¿Quién eres?" | Login con email + password |
| **Autorización** | Verificar QUÉ puedes hacer | "¿Qué puedes hacer?" | Admin puede borrar, User solo lee |

> **Clave:** Son dos controles distintos. Autenticarse no significa tener acceso a todo. Un usuario puede ser autenticado (sabemos quién es) pero no autorizado (no tiene permiso para esa acción).

## POR QUÉ importa

Las vulnerabilidades en autenticación son sumamente graves: permiten a atacantes acceder a información confidencial, controlar cuentas ajenas (incluso administradores) y expandir la superficie de ataque. Estas fallas ocurren por debilidades frente a fuerza bruta o por fallos de lógica y codificación que permiten evadir controles.

| Sin autenticación | Sin autorización |
|-------------------|------------------|
| Cualquiera accede a tu API | Un usuario normal puede borrar todo |
| No puedes rastrear quién hizo qué | No hay separación de roles |
| No puedes controlar permisos | Un atacante con token válido accede a todo |

---

## 1. Vulnerabilidades en Login Basado en Contraseñas

### Ataques de fuerza bruta

Adivinar combinaciones de usuario y contraseña de forma automatizada usando wordlists. Los atacantes optimizan con patrones predecibles (correos, `admin`, `administrator`) y adaptan diccionarios para cumplir políticas comunes (ej. `Mypassword1!`).

### Enumeración de usuarios

El sitio revela si un usuario existe, permitiendo generar listas de usuarios válidos. Se detecta por:

| Vector | Ejemplo |
|--------|---------|
| **Mensajes de error** | "Usuario no encontrado" vs "Contraseña incorrecta" — incluso diferencias sutiles en HTML oculto |
| **Códigos HTTP** | 404 para usuario inexistente vs 401 para contraseña mala |
| **Tiempos de respuesta** | Servidor verifica contraseña solo tras confirmar usuario → запросы con usuarios válidos tardan más |

> [!tip] Ataque de timing
> Los atacantes exageran la diferencia enviando contraseñas extremadamente largas que sobrecargan el procesamiento de hashes.

### Protección deficiente contra fuerza bruta

Muchos sistemas tienen fallos lógicos: restablecen el contador de intentos si el atacante inicia sesión exitosamente con su propia cuenta, insertando credenciales propias a intervalos regulares dentro de la lista de ataques.

### Bloqueo de cuentas

| Ventaja | Desventaja |
|---------|------------|
| Evita fuerza bruta dirigida | Facilita enumeración (delata si usuario existe) |
| | No protege contra credential stuffing (1 intento por cuenta) |
| | Permite DoS bloqueando masivamente cuentas válidas |

### Rate limiting esquivable

Los atacantes manipulan cabeceras HTTP para camuflar su IP real o envían múltiples intentos de contraseña en una única solicitud HTTP.

### Autenticación Básica HTTP

Intrínsecamente insegura: concatena usuario y contraseña y los codifica con Base64 en la cabecera `Authorization`. Sin HTTPS/HSTS, las credenciales se interceptan fácilmente. Carece de controles contra fuerza bruta y es vulnerable a CSRF.

---

## 2. Vulnerabilidades en MFA/2FA

El MFA es sustancialmente más seguro, pero su seguridad depende de la correcta implementación:

### El "falso" segundo factor

El uso de códigos enviados por correo **no constituye un segundo factor genuino**: el atacante que conozca la contraseña probablemente pueda vulnerar la del correo, verificando el factor de conocimiento en dos pasos de manera redundante.

### Códigos vía SMS

Transmitir códigos por SMS expone a intercepciones en tránsito o **SIM swapping**. Es preferible aplicaciones generadoras locales (Google Authenticator) o tokens físicos (YubiKey).

### Bypass simple de 2FA

Si la verificación de contraseña y el código ocurren en páginas independientes, el usuario queda en estado pre-autenticado. Muchas aplicaciones permiten navegar directamente a URLs internas sin que el backend verifique si completó el segundo factor.

### Lógica de verificación rota

Si la app no verifica que quien proporciona el código 2FA es el mismo que ingresó la contraseña:

```
1. Atacante inicia sesión con SUS credenciales
2. Modifica cookie de cuenta (account=victima) al enviar código 2FA
3. Si sistema procesa código para la cuenta de la cookie
4. Y no hay límites estrictos de intentos sobre el código (4-6 dígitos)
5. → Fuerza bruta sobre el código → acceso a cuenta de víctima
```

---

## 3. Vulnerabilidades en Gestión de Cuentas

### Cookies de "Recuérdame"

Si las cookies se generan concatenando valores predecibles (usuario, timestamp, contraseña) y se codifican con Base64, un atacante examinando su propia cookie deduce la fórmula y genera cookies falsas. Incluso con hashes, la falta de salt permite fuerza bruta local con bases de datos de hashes.

### Restablecimiento de contraseñas

| Vulnerabilidad | Consecuencia |
|----------------|-------------|
| Contraseñas generadas en texto claro por email | Interceptación en infraestructura de correo |
| Parámetros predecibles en URL (`?user=victima`) | Cambio de contraseña de terceros sin restricción |
| Token de alta entropía pero sin re-validación POST | Atacante elimina parámetro token y fuerza cambio |
| URL generada dinámica vía middleware | Password reset poisoning → redirige token a servidor del atacante |

> [!warning] Token robusto no es suficiente
> El token debe ser de alta entropía, temporizado y destruirse tras usarse. Pero además debe re-validarse al cargar el formulario Y al procesar el cambio final.

### Cambio de contraseña

Si el formulario no valida la contraseña actual o usa campos ocultos para identificar cuenta destino, un atacante manipula la petición HTTP para cambiar contraseñas arbitrariamente o enumerar usuarios.

---

## 4. Vulnerabilidades en OAuth 2.0

OAuth 2.0 delega acceso sin revelar credenciales, pero su flexibilidad facilita fallas de implementación graves.

> [!tip] Ver más
> Vulnerabilidades detalladas de OAuth: [[40 - RECURSOS/Conceptos/Vulnerabilidades-OAuth]]

---

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
> JWT solo está **firmado**, no cifrado. Cualquiera puede decodificar el payload (jwt.io). Nunca guardes información sensible en el payload.

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

---

## Cómo asegurar la autenticación

### 1. Credenciales en tránsito y reposo

Imponer redirección HTTP → HTTPS estricta. Auditar que nombres de usuario o correos no se filtren en perfiles públicos o respuestas del servidor.

### 2. Políticas de contraseña dinámicas

En lugar de reglas estáticas de complejidad (que generan contraseñas deducibles), usar validadores interactivos como **zxcvbn** de Dropbox, que evalúa complejidad real en tiempo real.

### 3. Prevención integral de enumeración

- Mensajes de error idénticos: "Usuario o contraseña incorrectos"
- Códigos de estado HTTP idénticos para usuarios válidos e inválidos
- Sincronizar tiempos de respuesta para que sean indistinguibles

### 4. Mitigación de fuerza bruta efectiva

- Rate limiting basado en IP real
- CAPTCHA tras límite razonable de intentos fallidos
- Proteger algoritmos contra manipulación de cabeceras de origen de IP
- Evitar lógica que restaure contadores de forma sospechosa

### 5. Re-validación de lógica y funciones auxiliares

Auditar cada flujo: cambios de contraseña, restablecimientos, cookies de sesión. Tokens de restablecimiento: alta entropía, temporales, destruirse tras usarse, re-validarse al cargar formulario Y al procesar cambio.

### 6. Implementación correcta de MFA

Usar aplicaciones de autenticación o tokens físicos (no SMS ni email). El backend debe validar estrictamente la correspondencia del token de segundo factor con el identificador del usuario que inició sesión.

---

## Referencia
- [[40 - RECURSOS/Conceptos/Vulnerabilidades-OAuth]]
- [[40 - RECURSOS/MOCs/MOC - Seguridad]]
- [[40 - RECURSOS/Conceptos/Anti-Patrones-Seguridad]]
- [[40 - RECURSOS/Conceptos/Rate-Limiting]]
- [[20 - PROYECTOS/DevSecOps-Proyecto/Fase-0-Seguridad/JWT-Setup]]

---
#seguridad #autenticacion #jwt #oauth #owasp #authorization #mfa #sesiones #fuerza-bruta
