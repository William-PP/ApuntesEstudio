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

## Clasificación de Vulnerabilidades de Autenticación

> [!tip] Referencia rápida
> Tabla maestra de todas las vulnerabilidades de autenticación según las fuentes de PortSwigger Web Security Academy.

| Tipo                     | Subtipo                           | Descripción                                                                                                                        | Fuente          |
| ------------------------ | --------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- | --------------- |
| **Contraseñas**          | Fuerza bruta                      | Prueba y error automatizada con wordlists para adivinar credenciales                                                               | [1][2][3][4][5] |
| **Contraseñas**          | Enumeración de usuarios           | Identificación de usuarios válidos por mensajes de error, códigos HTTP o timing                                                    | [1][2][3][4][5] |
| **Contraseñas**          | Protección defectuosa             | Bloqueo de cuentas/IP/rate limiting evadible por manipulación de IP, reinicio de contadores o múltiples credenciales por solicitud | [1][3][4][5]    |
| **Contraseñas**          | Credential stuffing               | Diccionarios masivos de credenciales filtradas, explotando reutilización de contraseñas                                            | [1]             |
| **MFA**                  | Bypass de 2FA                     | Omisión del segundo factor por fallas en lógica de acceso o flujo incompleto                                                       | [1][2][3][4][5] |
| **MFA**                  | Lógica de verificación defectuosa | Sistema no valida que token/MFA pertenezca al usuario que inicia sesión (cookies manipulables)                                     | [2][3][4][5]    |
| **MFA**                  | Fuerza bruta de códigos           | OTP de 4-6 dígitos sin límites estrictos de intentos                                                                               | [1][3][4][5]    |
| **MFA**                  | Intercepción SMS / SIM swapping   | Códigos vía SMS interceptados o desviados por clonación de SIM                                                                     | [3]             |
| **OAuth**                | Servicio OAuth                    | Fallas en redirect_uri, scope, registro no verificado, fuga de tokens vía proxy                                                    | [6][2][5]       |
| **OAuth**                | Aplicación cliente                | Flujo implicit sin validación backchannel, ausencia de parámetro state                                                             | [6][4]          |
| **OAuth**                | Terceros                          | Problemas de redirección, confianza y validación insuficiente de tokens externos                                                   | [1][2][4]       |
| **HTTP Basic**           | Exposición de credenciales        | Base64 reversible, exposición MITM, sin protección fuerza bruta ni CSRF                                                            | [1]             |
| **Sesión persistente**   | Cookies "Recuérdame"              | Tokens predecibles generados con datos estáticos → secuestro de sesión                                                             | [2][5]          |
| **Restablecimiento**     | Lógica rota                       | Parámetros predecibles, tokens sin re-validación POST, reset poisoning vía Host                                                    | [2][3][4][5]    |
| **Cambio de contraseña** | Manipulación de parámetros        | Campos ocultos para usuario destino permiten cambio arbitrario sin validación de sesión                                            | [2][4][5]       |

**Fuentes:**
[1] Password-based login · [2] Other auth mechanisms · [3] MFA · [4] Secure auth · [5] Auth vulnerabilities · [6] OAuth 2.0 — PortSwigger Web Security Academy

---

## 1. Vulnerabilidades en Login Basado en Contraseñas

En aplicaciones web con login basado en contraseñas, el conocimiento de la contraseña se considera prueba suficiente de identidad. Si un atacante obtiene o adivina las credenciales, la seguridad queda completamente comprometida.

### Ataques de fuerza bruta

Uso sistemático de prueba y error para adivinar credenciales válidas, automatizado con herramientas y wordlists. Los atacantes no prueban al azar: aplican lógica y conocimiento del comportamiento humano.

**Fuerza bruta sobre nombres de usuario:**
- Patrones predecibles: direcciones de correo corporativas (`nombre.apellido@empresa.com`)
- Cuentas de altos privilegios: `admin`, `administrator`, `root`
- Exposición pública: perfiles de usuario accesibles sin autenticación, respuestas HTTP que revelan correos de admins o soporte

**Fuerza bruta sobre contraseñas:**
- Los usuarios adaptan contraseñas débiles para cumplir políticas de complejidad: `Mypassword1!`, `Myp4$$w0rd`
- En rotaciones obligatorias, modificaciones mínimas y secuenciales: `Mypassword1!` → `Mypassword1?` → `Mypassword2!`
- Los atacantes diseñan diccionarios que simulan estas políticas (letras capitalizadas al inicio, números al final, caracteres especiales predecibles)

### Enumeración de nombres de usuario

El atacante identifica si un usuario existe observando cambios en comportamiento o respuestas de la app. Al obtener una lista de usuarios válidos, concentra la fuerza bruta solo en descifrar contraseñas.

| Vector | Mecanismo |
|--------|-----------|
| **Códigos de estado HTTP** | Si usuario válido devuelve código diferente, delata existencia |
| **Mensajes de error** | Diferencias sutiles incluso cuando se intentan mensajes idénticos — errores tipográficos, caracteres ocultos en HTML |
| **Tiempos de respuesta** | Servidor verifica contraseña solo si usuario es válido → paso adicional genera desviación temporal |

> [!tip] Ataque de timing amplificado
> Los atacantes exageran la diferencia enviando contraseñas extremadamente largas que requieren tiempo notable de hashing, haciendo el retraso evidente incluso con diferencias de milisegundos.

### Fallos en protección contra fuerza bruta

Las defensas implementadas suelen tener fallos lógicos que los atacantes evadir fácilmente:

**Bloqueo de IP esquivable por inicio de sesión exitoso:**
Algunas apps restablecen el contador de intentos fallidos de una IP cuando detectan un login exitoso. El atacante inserta sus propias credenciales válidas a intervalos regulares dentro de la lista de ataques, impidiendo que el límite se alcance.

**Bloqueo de cuentas con debilidades:**

| Protección | Vulnerabilidad |
|------------|----------------|
| Bloqueo por intentos fallidos | Facilita enumeración si el mensaje es explícito |
| | No protege contra **password spraying** |
| | No protege contra **credential stuffing** |
| | Permite DoS bloqueando masivamente cuentas válidas |

- **Password spraying (fuerza bruta inversa):** Probar una o dos contraseñas muy comunes en miles de usuarios. Como no se supera el límite por cuenta, el atacante prueba miles sin bloquear ninguna y compromete a aquellos con contraseñas débiles.
- **Credential stuffing:** Usar pares usuario/contraseña robados de otras filtraciones. Como la gente reutiliza credenciales, cada par se prueba solo una vez → el bloqueo individual nunca se activa.

**Rate limiting esquivable:**
- Manipulación de cabeceras HTTP para camuflar IP real
- Múltiples intentos de contraseña en una sola solicitud HTTP (el contador registra un solo envío)

### Autenticación Básica HTTP

Estándar antiguo que envía credenciales en cada solicitud: `Authorization: Basic base64(username:password)`. Intrínsecamente insegura:

| Vulnerabilidad | Detalle |
|----------------|---------|
| **Codificación reversible** | Base64 no es cifrado; cualquiera revierte para obtener texto claro |
| **Exposición en tránsito** | Sin HTTPS/HSTS, atacante intercepta vía MITM |
| **Sin controles de fuerza bruta** | Rara vez cuenta con protección integrada |
| **Vulnerable a CSRF** | Carece de mecanismos nativos contra falsificación de solicitudes |
| **Reutilización de credenciales** | Si el atacante vulnera una página poco interesante, reutiliza credenciales para acceder a secciones confidenciales

---

## 2. Vulnerabilidades en MFA/2FA

El MFA se ha convertido en estándar de seguridad crítico para mitigar debilidades de contraseñas. Al exigir al menos dos factores de naturaleza distinta — **algo que sabes** + **algo que tienes** — se reduce drásticamente la probabilidad de compromiso. Pero una implementación defectuosa puede eludirse tan fácilmente como un login sin MFA.

### MFA real vs. falso

Para que un sistema sea verdaderamente multifactorial, debe validar factores de **naturaleza diferente**:

| Tipo | Ejemplo | Seguridad |
|------|---------|-----------|
| **MFA Real** | Contraseña + código generado localmente en teléfono (Google Authenticator) | Factor conocimiento + posesión genuinos |
| **MFA Falso** | Contraseña + código enviado por correo electrónico | Mismo factor (conocimiento) verificado dos veces — si comprometen la contraseña del correo, todo cae |

> [!warning] El correo no es un segundo factor
> Acceder al código de verificación depende de conocer las credenciales del correo. Si la contraseña del correo es idéntica o similar a la de la aplicación, el esquema de seguridad cae de forma inmediata.

### Canales de transmisión de códigos

| Canal | Mecanismo | Riesgo |
|-------|-----------|--------|
| **Aplicación local** (Google Authenticator) | Código generado en el dispositivo sin transmisión | Mínimo — opción más segura |
| **Token físico** (YubiKey, token RSA) | Dispositivo de propósito específico | Mínimo — hardware dedicado |
| **SMS** | Código transmitido por red de telecomunicaciones | **Intercepción** de tráfico SMS + **SIM swapping** (atacante engaña a la compañía telefónica para obtener SIM con número de víctima) |

### Vulnerabilidad 1: Bypass por flujo incompleto

En muchas apps, el login se estructura en pasos secuenciales independientes: primero contraseña, luego código 2FA en pantalla separada.

**La falla:** El servidor coloca al usuario en estado "pre-autenticado" tras validar la contraseña, **antes** de recibir el código. Si no hay controles estrictos que restrinjan acceso hasta completar ambos pasos:

```
1. Atacante conoce contraseña de víctima
2. Ingresa contraseña → servidor marca como "pre-autenticado"
3. Ignora pantalla de 2FA
4. Navega directamente a /my-account o /dashboard
5. → Sistema carga recurso asumiendo login completo
```

### Vulnerabilidad 2: Lógica de verificación rota

Ocurre cuando la app **no verifica** que quien envía el código 2FA sea el mismo que ingresó la contraseña.

**Flujo típico del fallo:**

```
POST /login-steps/first
→ username=carlos&password=qwerty
→ Set-Cookie: account=carlos

GET /login-steps/second
→ Cookie: account=carlos

POST /login-steps/second
→ Cookie: account=carlos & verification-code=123456
```

**Explotación:**

```
1. Atacante inicia sesión con SUS credenciales legítimas
2. Recibe cookie de sesión activa
3. Al paso dos, intercepta petición y modifica cookie:
   Cookie: account=usuario_victima
4. Backend usa ciegamente el valor de la cookie alterada
5. Intenta validar código contra cuenta de víctima
6. → Fuerza bruta sobre código (4-6 dígitos) → acceso sin conocer contraseña
```

### Vulnerabilidad 3: Fuerza bruta sobre códigos 2FA

Los códigos temporales son números cortos de 4 o 6 dígitos — el espacio de búsqueda es extremadamente pequeño:

| Dígitos | Combinaciones | Tiempo estimado (1000/s) |
|---------|---------------|--------------------------|
| 4 | 10,000 | ~10 segundos |
| 6 | 1,000,000 | ~17 minutos |

**Defensas ineficaces:** Bloquear sesión tras N intentos fallidos es fácilmente evadible con automatización avanzada.

**Evasión con Burp Intruder + Macros:**
```
1. Configurar macro que repita el flujo completo en cada intento
2. Macro: login con contraseña → extraer cookie → ir a paso 2FA → probar código
3. Si sesión se cierra tras intento fallido → macro reinicia ciclo automáticamente
4. Turbo Intruder ejecuta esto a gran velocidad → rompe código antes de que expire
```

### Principio fundamental para asegurar 2FA

> [!warning] Re-validación en cada paso
> El backend no debe dar nada por sentado en transiciones entre páginas. Debe re-validar rigurosamente en **cada paso intermedio** que la identidad del usuario que inició la autenticación coincide exactamente con la que procesa y finaliza el flujo, bloqueando cualquier intento de alteración de cookies o parámetros de sesión.

---

## 3. Vulnerabilidades en Gestión de Cuentas

Además del login principal, las apps proporcionan funcionalidades complementarias (mantener sesión, restablecer, cambiar contraseña). Los desarrolladores suelen proteger el formulario de login pero descuidan estas funciones auxiliares. Un atacante puede registrar una cuenta propia para estudiar el comportamiento de estas páginas sin restricciones.

### Cookies de "Recuérdame" (Sesión Persistente)

La opción "Recordarme" genera un token persistente en cookie. Poseer esta cookie permite saltarse completamente la autenticación interactiva.

| Vulnerabilidad | Mecanismo |
|----------------|-----------|
| **Cookies basadas en datos estáticos** | Token construido concatenando nombre de usuario, timestamp o incluso la contraseña. Atacante registra cuenta, inspecciona su cookie, deduce la fórmula y genera cookies falsas por fuerza bruta |
| **Base64 o sin salt** | Base64 es reversible trivialmente. Incluso con hash (MD5/SHA), sin salt el atacante busca hashes en bases de datos públicas de contraseñas comunes — evadiendo rate limiting del sitio (las cookies no se contabilizan en límites de login) |
| **Robo vía XSS** | Si el sitio es vulnerable a XSS, atacante roba cookie "remember me" de víctima activa y analiza su estructura para deducir la fórmula general |
| **Frameworks mal configurados** | El formato exacto de la cookie puede estar documentado públicamente en repositorios de código abierto |

### Restablecimiento de contraseñas

Este flujo opera sin validación usual de contraseña, lo que lo hace intrínsecamente peligroso.

#### A. Envío de contraseñas por correo

| Vulnerabilidad | Consecuencia |
|----------------|-------------|
| **Contraseña en texto claro** | Si la app puede enviar la contraseña actual, significa que se almacena sin hashing — falla masiva |
| **Contraseña temporal por correo** | Correo no es canal cifrado; intercepción vía MITM. Bandejas sincronizadas en múltiples dispositivos incrementan exposición |

#### B. Restablecimiento vía URL

**Parámetros predecibles:**
```
http://vulnerable-site.com/reset?user=victim
```
Atacante cambia `user` por víctima → accede directamente al formulario de cambio.

**Fallo de verificación POST:**
```
1. Token de alta entropía generado: ?token=a0ba0d1cb...
2. Backend valida token al cargar formulario (GET) ✓
3. Backend NO valida token al enviar formulario (POST) ✗
4. Atacante carga formulario con su token legítimo
5. Al enviar (POST), modifica o elimina parámetro token
6. → Servidor aplica cambio en cuenta arbitraria
```

> [!warning] Token robusto no es suficiente
> El token debe ser de alta entropía, temporizado y destruirse tras usarse. Pero además debe re-validarse al cargar el formulario Y al procesar el cambio final.

**Password reset poisoning:**
Si la URL de restablecimiento se genera dinámicamente usando cabeceras manipulables (ej. `Host` header vía middleware), atacante altera la solicitud para que el enlace enviado a la víctima apunte a un servidor controlado por el atacante, capturando el token.

### Cambio de contraseña

El formulario de cambio típicamente requiere contraseña actual y nueva clave dos veces. Internamente usa los mismos componentes que el login, heredando las mismas debilidades.

| Vulnerabilidad | Mecanismo |
|----------------|-----------|
| **Campos ocultos para cuenta destino** | `<input type="hidden" name="username" value="victima">` — atacante modifica campo, backend confía a ciegas → enumeración o cambio arbitrario |
| **Sin validación de sesión** | Si el endpoint procesa cambios sin verificar autenticación activa, cualquier petición HTTP directa puede cambiar contraseñas |
| **Fuerza bruta heredada** | La validación de contraseña actual hereda las debilidades de timing y fuerza bruta del endpoint de login

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
