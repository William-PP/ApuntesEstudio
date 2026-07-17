---
tags: [proyecto/gestion-deportiva, database, diccionario-datos, devsecops]
created: 2026-07-14
updated: 2026-07-14
---

# GestionDeportiva — Diccionario de Datos

> Documentación en tablas del modelo relacional (SQL Server). Complementa el diagrama de clases ([[DevSecOps-Proyecto/GestionDeportiva-Diagrama-Clases]]).

---

## 1. Persona

Tabla base del patrón TPT. Toda persona registrada en el sistema, sea entrenador, deportista o ambas.

| Atributo | Tipo | Llave | Nulo | Nota |
|---|---|---|---|---|
| numero_documento | varchar(20) | **PK** | No | VARCHAR porque pasaportes y CE son alfanuméricos |
| tipo_documento | varchar(10) | | No | CHECK: CC \| PA \| CE \| TI |
| nombres | varchar(100) | | No | |
| apellidos | varchar(100) | | No | |
| fecha_nacimiento | date | | No | |
| sexo | char(1) | | Sí | CHECK: M \| F \| O |
| email | varchar(120) | UQ | Sí | UNIQUE filtrado (solo si no es NULL) |
| telefono | varchar(20) | | Sí | VARCHAR para permitir +57, espacios, guiones |
| direccion | varchar(200) | | Sí | |
| ciudad | varchar(80) | | Sí | |
| fecha_registro | datetime2 | | No | DEFAULT GETDATE() |
| activo | bit | | No | DEFAULT 1 |

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
| tipo_sangre | varchar(5) | | Sí | CHECK: A+ \| A- \| B+ \| B- \| AB+ \| AB- \| O+ \| O- |
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
| fecha_limite_inscripcion | date | | Sí | CHECK: <= fecha_inicio |
| sede | varchar(150) | | Sí | |
| direccion | varchar(200) | | Sí | |
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

**Índice único:** (id_deportista, id_torneo_modalidad, id_torneo_categoria) — `UQ_Inscripcion_Unica`

> [!warning] Gap de consistencia — `UQ_Inscripcion_Unica` no valida que ambas FK pertenezcan al mismo Torneo
> El índice único evita que el mismo deportista se inscriba dos veces en la misma combinación modalidad+categoría, pero **no valida** que `TorneoModalidad.id_torneo` y `TorneoCategoria.id_torneo` sean el mismo torneo. Ninguno de los tres índices únicos de este documento (`UQ_TorneoModalidad`, `UQ_TorneoCategoria`, `UQ_Inscripcion_Unica`) cierra ese gap — cada uno valida unicidad dentro de su propia tabla, no consistencia cruzada entre `TorneoModalidad` y `TorneoCategoria`. Dos soluciones posibles, detalladas en [[DevSecOps-Proyecto/GestionDeportiva-Diagrama-Clases]] § Notas de diseño: trigger de validación cruzada, o fusionar ambas tablas asociativas en una sola.

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

## Referencia

- [[DevSecOps-Proyecto/GestionDeportiva-Diagrama-Clases]] — diagrama UML equivalente, con las notas de diseño completas de los dos gaps señalados en §4 y §10
- [[DevSecOps-Estudio/Patrones/Best-Practices]]
- [[DevSecOps-Estudio/Patrones/Anti-Patrones-Seguridad]]
- [[DevSecOps-Estudio/Seguridad/Bases-Datos-Vulnerabilidades]]
- [[DevSecOps-Proyecto/00-Visión-General]]

---
#database #diccionario-datos #devsecops #gestion-deportiva #sql-server