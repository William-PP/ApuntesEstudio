# Anti-Patrones de Seguridad

## QUÉ es

Patrones de diseño que parecen correctos o convenientes pero son inseguros o problemáticos. Conocerlos te ayuda a evitarlos — y a reconocerlos en code review.

---

## 1. Hardcoded Secrets

```csharp
// ❌ MAL
var connectionString = "Server=localhost;Password=Admin123!";

// ✅ BIEN
var connectionString = _config.GetConnectionString("Default");
```

**Por qué es malo:** Si el código se filtra (repo público, laptop robada, acceso no autorizado), el secret también se filtra. Y en Git, incluso si lo borras después, queda en el historial de commits.

## 2. Sin Input Validation

```csharp
// ❌ MAL
var result = await _context.Users
    .FromSqlRaw($"SELECT * FROM Users WHERE Id = {userId}")
    .FirstOrDefaultAsync();

// ✅ BIEN
var result = await _context.Users
    .Where(u => u.Id == userId)
    .FirstOrDefaultAsync();
```

**Por qué es malo:** SQL injection, XSS, command injection. El interpolado de `$"...{userId}"` deja que el usuario inyecte SQL arbitrario; LINQ con `Where()` genera una query parametrizada automáticamente.

## 3. Authentication sin Authorization

```csharp
// ❌ MAL
app.UseAuthentication();
// No hay UseAuthorization() o no se usa [Authorize]

// ✅ BIEN
app.UseAuthentication();
app.UseAuthorization();

[Authorize]  // En cada controller/action
public class TeamController : ControllerBase { }
```

**Por qué es malo:** Cualquiera autenticado puede acceder a todo, sin importar su rol. Autenticación confirma identidad; autorización confirma permisos — son controles distintos y ambos son necesarios.

## 4. Exponer información en errores

```csharp
// ❌ MAL
catch (Exception ex)
{
    return StatusCode(500, ex.ToString()); // Stack trace visible
}

// ✅ BIEN
catch (Exception ex)
{
    _logger.LogError(ex, "Error occurred");
    return StatusCode(500, "Internal server error");
}
```

**Por qué es malo:** El stack trace revela arquitectura interna, nombres de clases, rutas de archivos y versiones de librerías — información de reconocimiento gratis para un atacante.

## 5. Secrets en logs

```csharp
// ❌ MAL
_logger.LogInformation("Connection string: {ConnectionString}", connectionString);

// ✅ BIEN
_logger.LogInformation("Connecting to database: {Server}", serverName);
```

**Por qué es malo:** Los secretos quedan en archivos de log, que suelen tener retención larga, backups, y a veces se envían a sistemas de monitoreo externos con menos control de acceso que la base de datos original.

## 6. Sin rate limiting en auth

```csharp
// ❌ MAL — sin rate limit
[HttpPost("login")]
public async Task<IActionResult> Login(LoginDTO dto) { ... }

// ✅ BIEN
[HttpPost("login")]
[EnableRateLimiting("auth")]
public async Task<IActionResult> Login(LoginDTO dto) { ... }
```

**Por qué es malo:** Ataque de fuerza bruta sin límite — un atacante puede probar miles de contraseñas por minuto contra el mismo endpoint.

## 7. CORS abierto

```csharp
// ❌ MAL
policy.AllowAnyOrigin().AllowAnyHeader().AllowAnyMethod();

// ✅ BIEN
policy.WithOrigins("https://tudominio.com")
      .AllowAnyHeader()
      .AllowAnyMethod();
```

**Por qué es malo:** Cualquier sitio web puede hacer requests a tu API desde el navegador de un usuario logueado, usando su sesión activa.

## 8. Container como root

```dockerfile
# ❌ MAL
FROM mcr.microsoft.com/dotnet/aspnet:10.0
WORKDIR /app
COPY . .
ENTRYPOINT ["dotnet", "MyApp.dll"]

# ✅ BIEN
FROM mcr.microsoft.com/dotnet/aspnet:10.0
RUN addgroup --system appgroup && adduser --system --ingroup appgroup appuser
WORKDIR /app
COPY --from=build /app/publish .
RUN chown -R appuser:appgroup /app
USER appuser
ENTRYPOINT ["dotnet", "MyApp.dll"]
```

**Por qué es malo:** Root dentro del contenedor puede hacer cualquier cosa en el sistema de archivos del contenedor, y si hay un container breakout, root dentro se traduce en más privilegios fuera.

## 9. Sin health checks

```csharp
// ❌ MAL — no sabes si la app está viva
app.MapControllers();

// ✅ BIEN
app.MapHealthChecks("/health");
app.MapControllers();
```

**Por qué es malo:** Kubernetes (o cualquier orquestador/balanceador) no puede detectar si la app falló, y sigue enviándole tráfico a una instancia caída o en mal estado.

## 10. Sin structured logging

```csharp
// ❌ MAL
Console.WriteLine("Error: " + ex.Message);

// ✅ BIEN
_logger.LogError(ex, "Error processing request for {UserId}", userId);
```

**Por qué es malo:** Logs en texto plano son inútiles para monitoreo automatizado — no se pueden filtrar, correlacionar ni alertar sobre ellos de forma confiable.

---

## Resumen — a qué categoría OWASP pertenece cada uno y qué lo detecta

| #   | Anti-patrón               | OWASP Top 10:2025                 | Qué lo detecta en el pipeline         |
| --- | ------------------------- | --------------------------------- | ------------------------------------- |
| 1   | Hardcoded Secrets         | A02 Security Misconfiguration     | Secret scanning (Gitleaks)            |
| 2   | Sin Input Validation      | A05 Injection                     | SAST (Semgrep/SonarQube)              |
| 3   | Auth sin Authorization    | A01 Broken Access Control         | Security tests / code review          |
| 4   | Exponer info en errores   | A02 Security Misconfiguration     | Code review, DAST                     |
| 5   | Secrets en logs           | A02 Security Misconfiguration     | Secret scanning, code review          |
| 6   | Sin rate limiting en auth | A01 Broken Access Control         | Security tests, pentesting            |
| 7   | CORS abierto              | A02 Security Misconfiguration     | SAST, DAST, code review               |
| 8   | Container como root       | A02 Security Misconfiguration     | Trivy scan, políticas de admisión K8s |
| 9   | Sin health checks         | A09 Logging and Alerting Failures | Code review                           |
| 10  | Sin structured logging    | A09 Logging and Alerting Failures | Code review                           |

> [!tip] Patrón general La mayoría de estos anti-patrones caen en **A02 Security Misconfiguration** — la categoría que en OWASP Top 10:2025 subió de #5 a #2. Tiene sentido: son errores de "olvido" o configuración por defecto, no fallas de diseño complejas — por eso son los más fáciles de prevenir con un checklist y los más comunes en la práctica.

---
#seguridad #anti-patrones #dotnet #devsecops #code-review 

---

## Referencia
- [[DevSecOps-Estudio/00-Index]]
- [[Patrones/Best-Practices]]
