---
tags: [proyecto/gestion-deportiva, database, diccionario-datos, devsecops]
created: 2026-07-14
updated: 2026-07-14
---

# GestionDeportiva — Diccionario de Datos

> Documentación en tablas del modelo relacional (SQL Server, v4). Complementa el diagrama de clases ([[DevSecOps-Proyecto/GestionDeportiva-Diagrama-Clases]]).

---

## 1. Persona

Tabla base del patrón TPT. Toda persona registrada en el sistema, sea entrenador, deportista o ambas.

| Atributo         | Tipo         | Llave  | Nulo | Nota                                             |
| ---------------- | ------------ | ------ | ---- | ------------------------------------------------ |
| numero_documento | varchar(20)  | **PK** | No   | VARCHAR porque pasaportes y CE son alfanuméricos |
| tipo_documento   | varchar(10)  |        | No   | CHECK: CC \| PA \| CE \| TI                      |
| nombres          | varchar(100) |        | No   |                                                  |
| apellidos        | varchar(100) |        | No   |                                                  |
| fecha_nacimiento | date         |        | No   |                                                  |
| sexo             | char(1)      |        | Sí   | CHECK: M \| F \| O                               |
| email            | varchar(120) | UQ     | Sí   | UNIQUE filtrado (solo si no es NULL)             |
| telefono         | varchar(20)  |        | Sí   | VARCHAR para permitir +57, espacios, guiones     |
| direccion        | varchar(200) |        | Sí   |                                                  |
| ciudad           | varchar(80)  |        | Sí   |                                                  |
| fecha_registro   | datetime2    |        | No   | DEFAULT GETDATE()                                |
| activo           | bit          |        | No   | DEFAULT 1                                        |

---

## 2. Entrenador

Subtipo de Persona (TPT). Comparte PK con Persona.

| Atributo | Tipo | Llave | Nulo | Nota |
|---|---|---|---|---|
| numero_documento | varchar(20) | **PK / FK** → Persona.numero_documento | No | Relación 1:1 |
| codigo_entrenador | varchar(40) | UQ | Sí | UNIQUE cuando existe |
| especialidad | varchar(100) | | Sí | |
| titulo | varchar(60) | | Sí | |
| fecha_titulo | date | | Sí | |
| institucion | varchar(120) | | Sí | |
| activo | bit | | No | DEFAULT 1 |

---

## 3. Deportista

Subtipo de Persona (TPT). Comparte PK con Persona.

| Atributo | Tipo | Llave | Nulo | Nota |
|---|---|---|---|---|
| numero_documento | varchar(20) | **PK / FK** → Persona.numero_documento | No | Relación 1:1 |
| tipo_sangre | varchar(20) | | Sí | CHECK: A+ \| A- \| B+ \| B- \| AB+ \| AB- \| O+ \| O- (DDL real usa varchar(20), sobredimensionado para valores de máx. 3 caracteres — ver §14) |
| peso | decimal(5,2) | | Sí | Kilogramos |
| altura | decimal(4,2) | | Sí | Metros |
| lesiones | varchar(max) | | Sí | Reemplaza TEXT deprecado |
| fecha_ingreso | date | | Sí | |
| activo | bit | | No | DEFAULT 1 |

---

## 4. EntrenadorDeportista

Entidad asociativa débil con histórico temporal entre Entrenador y Deportista.

| Atributo | Tipo | Llave | Nulo | Nota |
|---|---|---|---|---|
| id_entrenador_deportista | int | **PK** (IDENTITY) | No | Surrogate: la misma pareja puede repetirse en distintos períodos |
| id_entrenador | varchar(20) | **FK** → Entrenador.numero_documento | No | |
| id_deportista | varchar(20) | **FK** → Deportista.numero_documento | No | |
| fecha_inicio | date | | No | |
| fecha_fin | date | | Sí | NULL = relación vigente. CHECK: fecha_fin >= fecha_inicio |
| estado | varchar(30) | | No | CHECK: ACTIVA \| FINALIZADA \| SUSPENDIDA |
| observaciones | varchar(max) | | Sí | |

> [!warning] Gap de integridad — períodos solapados
> El `CHECK: fecha_fin >= fecha_inicio` valida que cada fila individual sea coherente, pero **no impide dos filas** para el mismo `id_entrenador`/`id_deportista` con rangos de fecha que se superponen entre sí. Si un deportista no debería tener dos entrenadores activos al mismo tiempo, hace falta un trigger de no-solapamiento. Detalle en [[DevSecOps-Proyecto/GestionDeportiva-Diagrama-Clases]] § Notas de diseño.

---

## 5. Torneo

| Atributo | Tipo | Llave | Nulo | Nota |
|---|---|---|---|---|
| id_torneo | int | **PK** (IDENTITY) | No | |
| nombre | varchar(120) | | No | |
| descripcion | varchar(max) | | Sí | |
| fecha_inicio | date | | No | CHECK: fecha_fin >= fecha_inicio |
| fecha_fin | date | | No | |
| fecha_inscripcion | date | | Sí | **Nombre real en DDL** (la documentación previa lo llamaba `fecha_limite_inscripcion`). CHECK: `fecha_inicio >= fecha_inscripcion` |
| sede | varchar(150) | | Sí | |
| direccion | varchar(200) | | **No** | DDL real la marca NOT NULL (documentación previa la tenía como opcional) |
| organizador | varchar(120) | | Sí | |
| estado | varchar(40) | | No | DEFAULT 'BORRADOR'. CHECK: BORRADOR \| INSCRIPCIONES_ABIERTAS \| INSCRIPCIONES_CERRADAS \| EN_CURSO \| FINALIZADO \| CANCELADO \| SUSPENDIDO |
| fecha_creacion | datetime2 | | No | DEFAULT GETDATE() |

---

## 6. Modalidad

| Atributo | Tipo | Llave | Nulo | Nota |
|---|---|---|---|---|
| id_modalidad | int | **PK** (IDENTITY) | No | |
| nombre | varchar(80) | | No | |
| tipo | varchar(30) | | Sí | Individual \| Parejas \| Equipo |
| activo | bit | | No | DEFAULT 1 |

---

## 7. Categoria

| Atributo | Tipo | Llave | Nulo | Nota |
|---|---|---|---|---|
| id_categoria | int | **PK** (IDENTITY) | No | |
| nombre | varchar(80) | | No | |
| div_edad | int | | Sí | Edad máxima en años |
| div_peso | decimal(5,2) | | Sí | Peso máximo en kg |
| sexo | char(1) | | Sí | CHECK: M \| F \| O |
| nivel | varchar(40) | | Sí | |
| descripcion | varchar(max) | | Sí | |

---

## 8. TorneoModalidad

Tabla asociativa Torneo↔Modalidad. Tiene PK propia porque es referenciada por Inscripcion.

| Atributo | Tipo | Llave | Nulo | Nota |
|---|---|---|---|---|
| id_torneo_modalidad | int | **PK** (IDENTITY) | No | Referenciado como FK desde Inscripcion |
| id_torneo | int | **FK** → Torneo.id_torneo | No | |
| id_modalidad | int | **FK** → Modalidad.id_modalidad | No | |
| nombre | varchar(40) | | Sí | Nombre personalizado para este torneo |
| estado | varchar(40) | | Sí | |
| observaciones | varchar(max) | | Sí | |

**Índice único:** (id_torneo, id_modalidad) — `UQ_TorneoModalidad`

---

## 9. TorneoCategoria

Tabla asociativa Torneo↔Categoria. Tiene PK propia porque es referenciada por Inscripcion.

| Atributo | Tipo | Llave | Nulo | Nota |
|---|---|---|---|---|
| id_torneo_categoria | int | **PK** (IDENTITY) | No | Referenciado como FK desde Inscripcion |
| id_torneo | int | **FK** → Torneo.id_torneo | No | |
| id_categoria | int | **FK** → Categoria.id_categoria | No | |
| observaciones | varchar(max) | | Sí | |

**Índice único:** (id_torneo, id_categoria) — `UQ_TorneoCategoria`

---

## 10. Inscripcion

Acto de inscribirse, previo al torneo. Sin campo de resultado/medalla (ver tabla Resultado).

| Atributo | Tipo | Llave | Nulo | Nota |
|---|---|---|---|---|
| id_inscripcion | int | **PK** (IDENTITY) | No | |
| id_deportista | varchar(20) | **FK** → Deportista.numero_documento | No | |
| id_entrenador | varchar(20) | **FK** → Entrenador.numero_documento | Sí | Puede inscribirse sin entrenador asignado |
| id_torneo_modalidad | int | **FK** → TorneoModalidad.id_torneo_modalidad | No | |
| id_torneo_categoria | int | **FK** → TorneoCategoria.id_torneo_categoria | No | |
| fecha_inscripcion | datetime2 | | No | DEFAULT GETDATE(). La hora importa para control de cupos |
| estado | varchar(40) | | No | DEFAULT 'PENDIENTE'. CHECK: PENDIENTE \| CONFIRMADA \| RETIRADA \| DESCALIFICADA \| NO_PRESENTADO |

**Índice único (DDL real):** `UQ_Inscripcion_Unica` — (id_Deportista, id_Entrenador, id_TorneoModalidad, id_TorneoCategoria)

> [!danger] Bug confirmado — el índice único no evita inscripciones duplicadas cuando no hay entrenador
> `id_Entrenador` es nullable ("puede inscribirse sin entrenador asignado", ver arriba) y **forma parte del índice único**. En SQL Server, `NULL` nunca se considera igual a `NULL` dentro de un índice único — así que dos filas con el mismo `id_Deportista` + misma `id_TorneoModalidad` + misma `id_TorneoCategoria`, ambas con `id_Entrenador = NULL`, **no violan la constraint** y se insertan sin problema. El deportista queda inscrito dos (o más) veces en la misma combinación torneo/modalidad/categoría cada vez que se inscribe sin entrenador — que, según la nota de la tabla, es un caso soportado explícitamente.
>
> **Fix:** sacar `id_Entrenador` del índice único. La unicidad de la inscripción debería depender solo de quién compite y en qué (deportista + modalidad + categoría), no de quién lo acompaña:
> ```sql
> ALTER TABLE Inscripcion DROP CONSTRAINT UQ_Inscripcion_Unica;
> ALTER TABLE Inscripcion ADD CONSTRAINT UQ_Inscripcion_Unica
>     UNIQUE (id_Deportista, id_TorneoModalidad, id_TorneoCategoria);
> ```

> [!warning] Gap de consistencia — `Inscripcion` con dos FKs a `Torneo` sin relación garantizada
> El índice único (aun corregido) evita inscripciones duplicadas, pero ni ese índice ni `UQ_TorneoModalidad_idTorneo_idModalidad` ni `UQ_TorneoCategoria_idTorneo_idCategoria` obligan a que ambas FK apunten al **mismo** `Torneo`. El trigger `tr_ValidarEstadoTorneo` (ver §15) tampoco lo valida — solo verifica el estado del torneo vía `TorneoModalidad`, nunca compara contra `TorneoCategoria`. Sigue siendo un gap real, no solo teórico. Dos formas de cerrarlo, detalladas en [[DevSecOps-Proyecto/GestionDeportiva-Diagrama-Clases]] § Notas de diseño: trigger de validación cruzada, o fusionar ambas tablas asociativas en una sola.

---

## 11. Resultado

Resultado obtenido, posterior al torneo. Relación 1:0..1 con Inscripcion.

| Atributo | Tipo | Llave | Nulo | Nota |
|---|---|---|---|---|
| id_resultado | int | **PK** (IDENTITY) | No | |
| id_inscripcion | int | **FK** → Inscripcion.id_inscripcion, UQ | No | UNIQUE garantiza relación 1:1 |
| medalla | varchar(20) | | Sí | CHECK: Oro \| Plata \| Bronce \| Diploma \| NULL |
| observaciones | varchar(max) | | Sí | Comentarios del jurado, incidencias, motivo de descalificación |
| fecha_registro | datetime2 | | No | DEFAULT GETDATE() |

> **Nota de diseño:** Resultado no tiene FK directa a Torneo, Deportista ni Categoria — esa información se navega vía Resultado → Inscripcion → resto del modelo. Duplicar esas FK violaría 3FN.

> [!warning] Riesgo de integridad, no solo de confidencialidad
> `medalla` es un dato que alguien podría querer falsificar (adulterar un resultado de competencia), no solo proteger de lectura no autorizada — ver mapeo OWASP en §13 (A08 Software or Data Integrity Failures). La escritura en esta tabla debería quedar restringida por rol y con audit log.

---

## 12. Tabla de relaciones (resumen)

| Tabla origen | Tabla destino | Cardinalidad | Vía |
|---|---|---|---|
| Persona | Entrenador | 1 : 0..1 | numero_documento |
| Persona | Deportista | 1 : 0..1 | numero_documento |
| Entrenador | EntrenadorDeportista | 1 : 0..* | id_entrenador |
| Deportista | EntrenadorDeportista | 1 : 0..* | id_deportista |
| Torneo | TorneoModalidad | 1 : 0..* | id_torneo |
| Modalidad | TorneoModalidad | 1 : 0..* | id_modalidad |
| Torneo | TorneoCategoria | 1 : 0..* | id_torneo |
| Categoria | TorneoCategoria | 1 : 0..* | id_categoria |
| Deportista | Inscripcion | 1 : 0..* | id_deportista |
| Entrenador | Inscripcion | 0..1 : 0..* | id_entrenador (nullable) |
| TorneoModalidad | Inscripcion | 1 : 0..* | id_torneo_modalidad |
| TorneoCategoria | Inscripcion | 1 : 0..* | id_torneo_categoria |
| Inscripcion | Resultado | 1 : 0..1 | id_inscripcion (UNIQUE) |

---

## 13. Seguridad del modelo — campos sensibles (DevSecOps)

| Campo / entidad | Tabla | Categoría de riesgo | OWASP Top 10:2025 | Mitigación al construir la API |
|---|---|---|---|---|
| `tipo_sangre`, `lesiones` | Deportista | Dato de salud | A04 Cryptographic Failures | Cifrado en reposo (column-level encryption o cifrado a nivel de aplicación antes de persistir) |
| `direccion`, `telefono`, `fecha_nacimiento` | Persona | Dato personal (Ley 1581) | A04 Cryptographic Failures | Cifrado en reposo; enmascarar en logs |
| `email` | Persona | Dato personal / identificador único | A04 Cryptographic Failures | No usar como identificador en URLs públicas; rate limiting en endpoints que lo consultan (enumeración de usuarios) |
| `numero_documento` | Persona (PK) | Dato personal, se propaga como FK a todo el modelo | A04 Cryptographic Failures | Nunca exponerlo crudo en respuestas de API ni en logs — es PK de `Persona` y FK en `EntrenadorDeportista`, `Inscripcion`, etc. |
| `Resultado.medalla` | Resultado | Integridad de datos, no confidencialidad | A08 Software or Data Integrity Failures | Audit log de quién y cuándo registró/modificó un resultado; solo el rol Organizador/Admin debería poder escribirlo |
| Endpoints de inscripción | Inscripcion | Abuso / spam de inscripciones | A01 Broken Access Control | Autorización por rol + rate limiting |

> [!tip] Checklist a aplicar
> Cuando diseñes la API sobre este esquema, [[DevSecOps-Estudio/Patrones/Best-Practices]] y [[DevSecOps-Estudio/Patrones/Anti-Patrones-Seguridad]] son la checklist a aplicar directamente sobre estos endpoints. El detalle de mitigación por endpoint (autorización, rate limiting, manejo de errores) está desarrollado ahí, no repetido acá.

---

## 14. Auditoría

Tabla de auditoría genérica, alimentada por triggers (§16). No estaba documentada en versiones previas de este diccionario.

| Atributo | Tipo | Llave | Nulo | Nota |
|---|---|---|---|---|
| id_auditoria | int | **PK** (IDENTITY) | No | |
| tabla | varchar(80) | | No | Nombre de la tabla auditada |
| operacion | varchar(10) | | Sí | CHECK: INSERT \| UPDATE \| DELETE |
| id_registro | varchar(80) | | Sí | PK del registro afectado, como string |
| campo | varchar(80) | | Sí | Solo se usa en updates campo-por-campo (ej. `tr_SuspenderRelacionesEntrenador`) |
| valor_anterior | varchar(300) | | Sí | |
| valor_nuevo | varchar(300) | | Sí | |
| usuario_db | varchar(80) | | No | DEFAULT `suser_sname()` |
| fecha_operacion | datetime2 | | No | DEFAULT `getdate()` |

> [!danger] PII en texto plano dentro de la auditoría
> `tr_AuditoriaPersona` (§16) escribe el **email** y el **número de documento** del usuario sin ningún tipo de enmascaramiento en `valor_nuevo` / `id_registro`. Esto contradice directamente la mitigación ya documentada en §13 para esos campos ("enmascarar en logs", "nunca loguearlo crudo"). Cualquier rol con acceso de lectura a `Auditoria` (o a la vista `vw_AuditoriaReciente`, §15) ve estos datos personales en claro. Además, el trigger solo cubre `INSERT`/`DELETE` sobre `Persona` — un `UPDATE` de `direccion`, `telefono` o `email` (los campos que §13 marca como sensibles) **no queda auditado en absoluto**.

---

## 15. Discrepancias encontradas entre esta documentación y el DDL real

Al comparar este diccionario contra el script de creación real (`script.sql`), se encontraron los siguientes puntos donde la documentación no coincidía con la base de datos:

| Punto | Documentación previa decía | DDL real dice | Impacto |
|---|---|---|---|
| `Deportista.tipo_sangre` | varchar(5) | varchar(20) | Bajo — sobredimensionado, no rompe nada, pero desperdicia espacio y permite valores más largos de los que el CHECK permitiría de todas formas |
| `Torneo.fecha_limite_inscripcion` | Ese nombre de columna | Se llama `fecha_inscripcion` en el DDL real | Medio — cualquier query o documentación que use el nombre viejo falla |
| `Torneo.direccion` | Nullable (Sí) | `NOT NULL` en el DDL real | Medio — un INSERT que omita `direccion` asumiendo que es opcional falla |
| `Deportista.activo` | (no señalado antes) | Es `NULL`-able, a diferencia de `Persona.activo` y `Entrenador.activo` que son `NOT NULL` | Bajo — inconsistencia de diseño entre las tres tablas TPT, mismo campo semántico con distinta nulabilidad |
| `Persona.sexo` CHECK | Documentado como M \| F \| O | DDL real (`CHK_Persona_Sexo`) solo permite M \| F — sin O | Medio — `Categoria.sexo` sí permite O (`CHK_Categoria_Sexo`), inconsistencia entre tablas relacionadas del mismo dominio |
| `Inscripcion` índice único | Documentado como 3 columnas (sin `id_Entrenador`) | DDL real tiene 4 columnas, incluye `id_Entrenador` | **Alto** — bug de integridad real, ver §10 |

---

## 16. Objetos adicionales encontrados en el DDL (no documentados antes)

### Funciones escalares (UDF)

| Función | Firma | Qué hace |
|---|---|---|
| `fn_CalcularEdad` | `(@fNac date) → int` | Edad exacta a hoy, ajustando si el cumpleaños de este año aún no ha ocurrido |
| `fn_NombreCompleto` | `(@doc varchar(20)) → varchar(200)` | `nombres + ' ' + apellidos` desde `Persona` |
| `fn_NombreModalidad` | `(@idMod int) → varchar(80)` | Lookup de nombre en `Modalidad` |
| `fn_TorneoVigente` | `(@idTorneo int) → varchar(5)` | `'SI'`/`'NO'` según si hoy está entre `fecha_inicio` y `fecha_fin` (nota: más idiomático como `bit`, pero funciona) |
| `fn_TotalMedallasDeportistas` | `(@doc varchar(20)) → int` | Cuenta resultados con `medalla IS NOT NULL` para un deportista |
| `fnCalcularIMC` | `(@peso decimal, @altura decimal) → decimal(5,2)` | `peso / altura²` — **nombre inconsistente**: es la única función sin guion bajo tras `fn` (el resto usa `fn_`) |

### Vistas

| Vista | Para qué |
|---|---|
| `vw_FichaDeportista` | Persona + Deportista + edad calculada |
| `vw_TorneosModalidades` | Torneo + Modalidad + estado |
| `vw_Medallero` | Resultado + Inscripcion + nombre del deportista + torneo |
| `vw_InfoEntrenadores` | Persona + Entrenador |
| `vw_InscripcionesPendientes` | Inscripciones en estado PENDIENTE con nombre de deportista y torneo |
| `vw_AuditoriaReciente` | Top 100 de `Auditoria`, más reciente primero — **hereda el problema de PII en claro de §14** |

### Stored Procedures

| Procedure | Qué hace | Nota |
|---|---|---|
| `sp_RegistrarDeportista` | INSERT transaccional en `Persona` + `Deportista` | |
| `sp_RegistrarEntrenador` | INSERT transaccional en `Persona` + `Entrenador` | |
| `sp_RegistrarResultado` | INSERT en `Resultado` | Sin control de rol — la autorización debe imponerse en la capa de API (ver §13) |
| `sp_AsignarEntrenador` | INSERT en `EntrenadorDeportista` | No valida solapamiento de fechas antes de insertar — instancia real del gap de §4 |
| `sp_ConsultarInscritosTorneo` | SELECT de inscripciones por torneo | |
| `sp_ActualizarEstadoTorneo` | UPDATE de `Torneo.estado` | |

### Triggers

| Trigger | Tabla | Evento | Qué hace |
|---|---|---|---|
| `tr_SuspenderRelacionesEntrenador` | Entrenador | AFTER UPDATE | Si `activo` pasa a 0, suspende automáticamente las relaciones `EntrenadorDeportista` en estado ACTIVA y audita el cambio |
| `tr_NoReabrirInscripcion` | Inscripcion | FOR UPDATE | Impide volver a `PENDIENTE` una inscripción ya `CONFIRMADA`/`RETIRADA`/`DESCALIFICADA`/`NO_PRESENTADO` |
| `tr_ValidarDeportistaActivo` | Inscripcion | FOR INSERT | Bloquea inscribir a un deportista con `activo = 0` |
| `tr_ValidarDeportistaEnInscripcion` | Inscripcion | FOR UPDATE | Bloquea pasar a `CONFIRMADA` si falta `peso` o `altura` del deportista |
| `tr_ValidarEstadoTorneo` | Inscripcion | FOR INSERT | Bloquea inscribir si el torneo (vía `TorneoModalidad`) no está en `INSCRIPCIONES_ABIERTAS` — no valida `TorneoCategoria`, ver §10 |
| `tr_AuditoriaPersona` | Persona | AFTER INSERT, DELETE | Ver §14 — problema de PII en claro |

---

## Referencia

- [[DevSecOps-Proyecto/GestionDeportiva-Diagrama-Clases]] — diagrama UML equivalente, con las notas de diseño completas de los dos gaps señalados en §4 y §10
- [[DevSecOps-Estudio/Patrones/Best-Practices]]
- [[DevSecOps-Estudio/Patrones/Anti-Patrones-Seguridad]]
- [[DevSecOps-Estudio/Seguridad/Bases-Datos-Vulnerabilidades]]

---
#database #diccionario-datos #devsecops #gestion-deportiva #sql-server
