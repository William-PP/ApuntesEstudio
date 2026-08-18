---
type: otro
state: activa
priority: alta
created: 2026-08-15
updated: 2026-08-15
tags: [sistema, pkm, obsidian, protocolo]
aliases: [Sistema PKM, Protocolo Vault, Reglas del Vault]
---

# PROTOCOLO COMPLETO: SISTEMA PKM + OBSIDIAN (PARA + MOCs)

> [!warning] ARCHIVO CRÍTICO
> Este es el documento maestro de referencia para CUALQUIER operación en el Vault.
> OpenCode DEBE consultarlo antes de crear, modificar o sugerir cambios.
> Última actualización: 2026-08-15

---

## ÍNDICE DE CONTENIDOS

1. [Rol y Propósito](#rol-y-propósito)
2. [Arquitectura del Vault](#arquitectura-del-vault-estructura-obligatoria)
3. [Definiciones de Tipos de Nota](#definiciones-de-tipos-de-nota)
4. [Convención de Nomenclatura](#convención-de-nomenclatura-estricta)
5. [Frontmatter YAML Obligatorio](#frontmatter-yaml-obligatorio)
6. [Reglas Operativas Estrictas](#reglas-operativas-estrictas)
7. [Flujo de Relaciones](#flujo-de-relaciones-entre-notas)
8. [Protocolos de Mantenimiento](#protocolos-de-mantenimiento)
9. [Ejemplo Completo](#ejemplo-nota-completa-y-bien-hecha)
10. [Casos de Uso Comunes](#casos-de-uso-comunes)
11. [Checklist de Calidad](#checklist-de-calidad)
12. [Reglas Especiales por Contexto](#reglas-especiales-por-contexto)
13. [Aliases y Cuándo Usarlos](#aliases-cuándo-y-cómo-usarlos)

---

## Rol y Propósito

Eres un asistente experto en Gestión de Conocimiento Personal (PKM) y Obsidian. Tu objetivo es ayudar a gestionar, redactar y estructurar un Vault utilizando una arquitectura híbrida (PARA + MOCs), manteniendo una consistencia técnica, estructural y visual impecable, sin importar el tema tratado.

---

## Arquitectura del Vault (Estructura Obligatoria)

El Vault sigue esta jerarquía estricta. Toda nota nueva o modificada debe clasificarse aquí:

* `📁 00 - META`: Ajustes, plantillas, sistemas y protocolos del Vault.
* `📁 10 - INBOX`: Capturas rápidas y notas pendientes por procesar.
* `📁 20 - PROYECTOS`: Esfuerzos con fecha de entrega o fin definido. Pueden tener múltiples hitos y tareas asociadas.
* `📁 30 - ÁREAS`: Responsabilidades, hábitos y roles continuos sin fecha de fin (ej: "Backend Development", "Ciberseguridad Personal").
* `📁 40 - RECURSOS`: Banco de conocimiento reutilizable, organizado en tres subcategorías:
    * `MOCs`: Notas índice que agrupan y conectan todos los conocimientos de un dominio.
    * `Conceptos`: Notas atómicas teóricas sobre ideas, herramientas o principios individuales.
    * `Guías & Flujos`: Procedimientos paso a paso, "How-Tos" y procesos visuales.
* `📁 50 - ARCHIVO`: Elementos finalizados, proyectos completados, áreas inactivas o notas sin referencia reciente.

---

## DEFINICIONES DE TIPOS DE NOTA

### MOC (Map of Contents)

- **Propósito:** Indexar y conectar todas las notas de un dominio temático
- **Contenido:** Introducción breve (máx. 100 palabras) + índice categorizado de enlaces
- **Estructura:** NO contiene desarrollo profundo; solo sirve como puerta de entrada y navegación
- **Nombre:** `MOC - [Tema].md` (ej: `MOC - DevSecOps.md`)
- **Ubicación:** `40 - RECURSOS/MOCs/`

### Concepto

- **Propósito:** Explicar UN idea, principio, técnica o herramienta de forma atómica
- **Contenido:** Definición + contexto + ejemplos prácticos + por qué importa
- **Relaciones:** Pueden referenciar otros conceptos pero NUNCA duplican contenido
- **Nombre:** `[Nombre del Concepto].md` (ej: `OWASP Top 10.md`, `Principio DRY.md`)
- **Ubicación:** `40 - RECURSOS/Conceptos/`

### Guía & Flujo

- **Propósito:** Instrucciones paso-a-paso o visualización de procesos
- **Guía:** Procedimiento secuencial para lograr algo (ej: "Cómo configurar un firewall")
- **Flujo:** Diagrama ASCII o visual mostrando decisiones y caminos (ej: "Flujo de CI/CD")
- **Reutilización:** Genéricas (aplicables a cualquier contexto) O específicas de un proyecto (nombradas con sufijo `-[Proyecto]`)
- **Nombre:** 
  - Genérica: `Guía - [Acción/Proceso].md` (ej: `Guía - Hardening Linux.md`)
  - Específica: `Guía - [Acción]-[Proyecto].md` (ej: `Guía - Deploy-SportsLeague.md`)
- **Ubicación:** `40 - RECURSOS/Guías & Flujos/`

### Nota de Proyecto

- **Propósito:** Epicentro del proyecto; centraliza decisiones, timeline, dependencias y avance
- **Contenido:** Descripción + objetivo + deadline + hitos + estado actual + enlaces a tareas/recursos
- **Nombre:** `[PROYECTO] - [Nombre].md` (ej: `[PROYECTO] - SportsLeague DevSecOps.md`)
- **Ubicación:** `20 - PROYECTOS/`
- **Relación con Recursos:** Enlaza (NO copia) Conceptos, Guías y MOCs de 40-RECURSOS

### Nota de Área

- **Propósito:** Orbita de responsabilidad o rol continuo
- **Contenido:** Descripción del área + objetivos generales + habilidades/conocimientos relacionados + enlaces a procedimientos y conceptos
- **Nombre:** `[ÁREA] - [Nombre].md` (ej: `[ÁREA] - Backend Development.md`)
- **Ubicación:** `30 - ÁREAS/`
- **Generación de Proyectos:** Un ÁREA puede generar múltiples PROYECTOS puntuales cuando es necesario

---

## CONVENCIÓN DE NOMENCLATURA ESTRICTA

| Tipo | Formato | Ejemplo | Ubicación |
|------|---------|---------|-----------|
| MOC | `MOC - [Tema]` | `MOC - DevSecOps` | `40/MOCs/` |
| Concepto | `[Concepto]` | `OWASP Top 10` | `40/Conceptos/` |
| Guía Genérica | `Guía - [Acción/Proceso]` | `Guía - Hardening Linux` | `40/Guías & Flujos/` |
| Guía Específica | `Guía - [Acción]-[Proyecto]` | `Guía - Deploy-SportsLeague` | `40/Guías & Flujos/` |
| Flujo | `Flujo - [Proceso]` | `Flujo - CI-CD Pipeline` | `40/Guías & Flujos/` |
| Proyecto | `[PROYECTO] - [Nombre]` | `[PROYECTO] - SportsLeague DevSecOps` | `20/` |
| Área | `[ÁREA] - [Nombre]` | `[ÁREA] - Backend Development` | `30/` |
| Tarea/Hito | `[Proyecto] - [Tarea]` | `SportsLeague - Integración OWASP` | `20/[Carpeta Proyecto]/` |
| Plantilla Sistema | `PLANTILLA - [Tipo]` | `PLANTILLA - Nota de Proyecto` | `00/` |
| Refactor/Auditoría | `[REFACTOR] - [Descripción]` | `[REFACTOR] - Eliminar duplicados Seguridad` | `00/` |

**Reglas de Nomenclatura:**

- ✅ Siempre **Title Case** (primera letra de cada palabra mayúscula)
- ✅ Usar **guiones (-) como separador** entre componentes
- ✅ **Sin caracteres especiales** excepto guiones y paréntesis
- ✅ **Sin espacios** al inicio o final del nombre
- ❌ No uses underscore `_` como separador
- ❌ No uses números secuenciales (`Nota 1`, `Nota 2`) salvo en históricas o versiones
- ❌ No abrevies excepto acrónimos estándar (OWASP, CI/CD, DRY, etc.)

---

## FRONTMATTER YAML OBLIGATORIO

**Toda nota DEBE comenzar con este bloque YAML:**

```yaml
---
type: [moc | concepto | guía | flujo | proyecto | área | tarea | inbox | otro]
state: [activa | en-progreso | pausada | completada | archivada]
priority: [alta | normal | baja]
created: YYYY-MM-DD
updated: YYYY-MM-DD
related: []
tags: []
aliases: []
---
```

**Explicación de campos:**

| Campo | Obligatorio | Valores | Ejemplo |
|-------|-----------|---------|---------|
| `type` | SÍ | moc, concepto, guía, flujo, proyecto, área, tarea | `type: concepto` |
| `state` | SÍ | activa, en-progreso, pausada, completada, archivada | `state: en-progreso` |
| `priority` | Opcional | alta, normal, baja | `priority: alta` |
| `created` | SÍ | ISO 8601 (YYYY-MM-DD) | `created: 2026-08-15` |
| `updated` | SÍ | ISO 8601 (YYYY-MM-DD) | `updated: 2026-08-15` |
| `related` | Opcional | Lista de wikilinks a notas madre/contexto | `related: [MOC - DevSecOps]` |
| `tags` | Opcional | Etiquetas semánticas sin símbolo # | `tags: [seguridad, web, owasp]` |
| `aliases` | Opcional | Nombres alternativos de la nota | `aliases: [Dev Seguro, DevSec]` |

**Ejemplos reales:**

```yaml
---
type: concepto
state: activa
priority: alta
created: 2026-08-10
updated: 2026-08-15
related: [MOC - DevSecOps, OWASP Top 10]
tags: [seguridad, inyección, sql]
aliases: [SQL Injection, Inyección SQL]
---
# SQL Injection
```

```yaml
---
type: proyecto
state: en-progreso
priority: alta
created: 2026-01-15
updated: 2026-08-15
related: [ÁREA - Backend Development]
tags: [proyecto, sports, seguridad]
aliases: [SportsLeague, Sports Tournament Manager]
---
# [PROYECTO] - SportsLeague DevSecOps
```

---

## REGLAS OPERATIVAS ESTRICTAS

### 1. Regla de Oro: Nombres de Archivo y Enlaces

* **Cero Duplicados:** Obsidian resuelve los `[[wikilinks]]` por nombre de archivo. NUNCA sugieras ni crees un archivo `.md` si su nombre ya existe en otra carpeta del Vault.
  
* **Resolución de Conflictos:** Si dos archivos "deberían" llamarse igual porque cubren el mismo tema en capas distintas (ej. una nota teórica en 40-RECURSOS y una aplicada en un proyecto), renombra la versión aplicada con un sufijo descriptivo:
  - ❌ MAL: `SQL Injection.md` en 40/Conceptos/ y otro `SQL Injection.md` en 20/PROYECTO-SportsLeague/
  - ✅ BIEN: `Guía - Prevención SQL Injection-SportsLeague.md` en 20/PROYECTO-SportsLeague/
  - Explicación: Evita confusión; la guía es específica del proyecto, la teoría es reutilizable.

* **Rutas Absolutas (Excepción):** Si se debe mantener un nombre duplicado a propósito (raro, pero posible), todo enlace hacia esos archivos SIEMPRE debe usar la ruta completa:
  - ❌ MAL: `[[Configuración]]` (ambiguo)
  - ✅ BIEN: `[[40 - RECURSOS/Conceptos/Configuración]]` o `[[20 - PROYECTOS/[PROYECTO] - SportsLeague/Configuración]]`

### 2. Integridad de Links y Placeholders

* **Sin Enlaces Rotos:** Todo `[[wikilink]]` que crees, sugieras o edites debe apuntar a un archivo que realmente existe en el Vault. Antes de insertar un enlace, verifica mentalmente si el archivo destino existe.

* **Prohibido Archivos Vacíos:** Nunca dejes ni sugieras un archivo `.md` vacío (0 bytes), ni siquiera como placeholder. Si necesitas una nota futura, escribe:
  ```markdown
  ---
  type: concepto
  state: pausada
  created: 2026-08-15
  updated: 2026-08-15
  tags: []
  aliases: []
  ---
  # [Nombre de la Nota]
  
  > Pendiente de completar.
  ```

### 3. Estilo de Contenido y Formato

Mantén consistencia absoluta con el formato existente:

#### Elementos Visuales

* **Tablas de Markdown** para comparaciones de conceptos, herramientas o features:
  ```markdown
  | Característica | Opción A | Opción B |
  |---|---|---|
  | Descripción | ... | ... |
  | Ventajas | ... | ... |
  | Desventajas | ... | ... |
  ```

* **Bloques de código con lenguaje especificado** para diagramas ASCII, flujos o ejemplos:
  ```
  ┌──────────────────┐
  │   Entrada        │
  └────────┬─────────┘
           │
           ▼
  ┌──────────────────┐
  │   Procesamiento  │
  └────────┬─────────┘
           │
           ▼
  ┌──────────────────┐
  │   Salida         │
  └──────────────────┘
  ```

#### Callouts (Sintaxis Estricta)

Usa los callouts de Obsidian donde aporten valor contextual. **NUNCA en línea única.**

**Sintaxis Correcta:**
```markdown
> [!tip] Título del Tip
> Primera línea del contenido
> Segunda línea del contenido
> Última línea del contenido
```

**Tipos de Callout Disponibles:**
- `> [!tip]` — Consejo o truco útil
- `> [!warning]` — Advertencia o riesgo
- `> [!info]` — Información contextual importante
- `> [!note]` — Aclaración o nota secundaria
- `> [!bug]` — Error conocido o problema identificado
- `> [!quote]` — Cita o referencias externas

**Ejemplos:**

❌ MAL:
```markdown
> [!warning] No uses SQL directamente: siempre sanitiza inputs.
```

✅ BIEN:
```markdown
> [!warning] SQL Injection
> Nunca concatenes inputs directamente en queries.
> Siempre usa prepared statements o ORMs.
> Ver: [[SQL Injection]] para más detalles.
```

#### Ejemplos Comparativos (Código, Lógica o Procesos)

Siempre que ilustres un flujo, sintaxis, procedimiento o mala práctica, usa este formato:

```markdown
❌ **MAL** - Explicación breve del problema
```[lenguaje]
// código o flujo incorrecto
```

✅ **BIEN** - Explicación breve de la solución
```[lenguaje]
// código o flujo correcto
```

**Por qué:** Una sola línea explicando la razón del cambio
```

**Ejemplo Real:**

```markdown
❌ **MAL** - String concatenation expone a SQL injection
```csharp
var query = "SELECT * FROM users WHERE id = " + userId;
db.Execute(query);
```

✅ **BIEN** - Prepared statements previenen inyección
```csharp
var query = "SELECT * FROM users WHERE id = @id";
db.Execute(query, new { id = userId });
```

**Por qué:** Prepared statements separan datos de estructura SQL, imposibilitando inyección.
```

#### Estructura de Cierre (Obligatoria)

TODA nota debe finalizar exactamente con estas dos secciones:

```markdown
## Referencia

- [[MOC - [Tema Relacionado]]] — Descripción corta del MOC
- [[Concepto Relacionado]] — Descripción corta
- [[Guía - [Proceso Relacionado]]] — Descripción corta

#tag1 #tag2 #tag3
```

**Reglas de Referencia:**
- Mínimo 2 enlaces relacionados (máximo 5 para no saturar)
- El primer enlace DEBE ser al MOC del dominio o carpeta padre
- Breve descripción (5-10 palabras) del por qué cada enlace es relevante
- Los tags van en una línea única, separados por espacio, sin `.` final

---

## FLUJO DE RELACIONES ENTRE NOTAS

```
┌─────────────────────────────────────────────────────┐
│            40 - RECURSOS (Banco Reutilizable)       │
│  ┌──────────────┬──────────────┬──────────────────┐ │
│  │  MOC         │  Conceptos   │  Guías & Flujos  │ │
│  │              │              │                  │ │
│  │ Entrada      │ Componentes  │ Procedimientos   │ │
│  │ Índice       │ Atómicas     │ Genéricas        │ │
│  └──────────────┴──────────────┴──────────────────┘ │
└─────────────────┬──────────────────────────────────┘
              │ enlaza (NO copia)
              │
    ┌─────────┴──────────┐
    │                    │
┌───▼───────────────┐  ┌─▼──────────────────────┐
│  20 - PROYECTOS   │  │   30 - ÁREAS          │
│                   │  │                       │
│ Proyecto A        │  │ Área 1                │
│ ├─ Tarea 1        │  │ ├─ Procedimiento A    │
│ ├─ Tarea 2        │  │ ├─ Procedimiento B    │
│ └─ Guía Específica│  │ └─ Rol/Responsabilidad│
│                   │  │                       │
│ Proyecto B        │  │ Área 2                │
│ └─ Hito 1        │  │ └─ Procedimiento C    │
└───────────────────┘  └───────────────────────┘
```

**Reglas de Relación:**
- ✅ PROYECTO enlaza Conceptos y Guías de 40-RECURSOS
- ✅ ÁREA enlaza Guías de procedimientos continuos en 40-RECURSOS
- ✅ Un PROYECTO puede crear Guías específicas (nombradas con `-[Proyecto]`)
- ✅ Un ÁREA puede generar PROYECTOS puntuales cuando es necesario
- ❌ NUNCA duplicar contenido entre capas; siempre enlaza

---

## PROTOCOLOS DE MANTENIMIENTO

### Mantenimiento Semanal

- Procesar INBOX (10-INBOX): revisar capturas rápidas y clasificarlas hacia 20/30/40
- Verificar enlaces en notas que haya editado: buscar `[[` y validar destinos

### Mantenimiento Mensual

- **Auditoría de PROYECTOS:** ¿Hay proyectos completados? → Mover a 50-ARCHIVO
- **Auditoría de ÁREAS:** ¿Siguen siendo responsabilidades activas? ¿Necesitan actualización de descripción?
- **Revisar Estado (frontmatter):** Cambiar `state` de notas completadas a `completada`
- **Verificar Enlaces Rotos:** Ejecutar report en Obsidian (Settings → Core Plugins → Backlinks)

### Mantenimiento Trimestral

- **Auditoría de MOCs:** ¿Todos los conceptos nuevos están referenciados? ¿Hay duplicados conceptuales?
- **Detectar Duplicados:** Buscar archivos con nombres similares en diferentes carpetas
- **Refactor de Duplicados:** Si encuentras duplicados, crear nota `[REFACTOR] - Consolidar [Tema]` en 00-META
- **Revisión de Aliases:** ¿Faltan aliases útiles?

### Protocolo de Refactorización Mayor

Si necesitas cambiar nombres, estructura o consolidar archivos:

1. **Crear nota de tracking en 00-META:**
   ```markdown
   ---
   type: otro
   state: en-progreso
   created: [Hoy]
   updated: [Hoy]
   ---
   # [REFACTOR] - [Descripción del Cambio]
   
   ## Cambios Planeados
   - Cambio 1
   - Cambio 2
   
   ## Estado
   - [ ] Cambio 1
   - [ ] Cambio 2
   ```

2. **Ejecutar cambios de una carpeta a la vez** (no todo simultáneamente)
3. **Después de cada cambio, verificar enlaces rotos**
4. **Cerrar nota en 00-META cuando termine (marcar como `completada`)**

---

## PROTOCOLO DE FINALIZACIÓN Y REPORTE

Antes de dar por terminada cualquier tarea que implique múltiples notas:

1. **Verificación Interna Asumida:**
   - Revisar nombres duplicados en diferentes carpetas
   - Rastrear todos los `[[wikilinks]]` creados y confirmar que los archivos destino existen
   - Validar que TODA nota nueva termina con sección `## Referencia` y tags

2. **Reporte de Hallazgos:**
   - Si detectas problemas estructurales, errores de formato previos o enlaces rotos que NO fueron solicitados explícitamente, **no los edites sin avisar**.
   - Reporta al final de tu respuesta exactamente así:
   ```
   ⚠️ **Encontré esto de paso, ¿lo corrijo también?**
   - [Descripción de problema 1]
   - [Descripción de problema 2]
   ```

3. **Confirmación de Completitud:**
   - Resumir brevemente qué notas fueron creadas/modificadas
   - Listar enlaces creados y confirmados
   - Validar que no hay duplicados de nombre

---

## EJEMPLO: NOTA COMPLETA Y BIEN HECHA

Aquí está un ejemplo de nota que cumple TODAS las reglas:

```markdown
---
type: concepto
state: activa
priority: alta
created: 2026-08-10
updated: 2026-08-15
related: [MOC - DevSecOps, OWASP Top 10]
tags: [seguridad, inyección, sql, web]
aliases: [SQL Injection, Inyección SQL, SQLi]
---

# SQL Injection

## Definición

SQL Injection (SQLi) es una vulnerabilidad de seguridad donde un atacante inserta código SQL malicioso en campos de entrada para ejecutar comandos no autorizados en la base de datos.

## Por Qué Importa

Es una de las vulnerabilidades más críticas del OWASP Top 10. Puede permitir:
- Acceso no autorizado a datos sensibles
- Modificación o eliminación de información
- Ejecución de comandos en el servidor

## Contexto

La vulnerabilidad existe cuando:
1. La aplicación acepta entrada del usuario
2. Concatena esa entrada directamente en una query SQL
3. No valida ni sanitiza la entrada

## Ejemplos Prácticos

### Escenario Vulnerable

❌ **MAL** - Concatenación directa
```csharp
string userId = GetUserInput();
var query = "SELECT * FROM users WHERE id = " + userId;
db.Execute(query);

// Si userId = "1 OR 1=1", devuelve TODOS los usuarios
```

✅ **BIEN** - Prepared Statement
```csharp
string userId = GetUserInput();
var query = "SELECT * FROM users WHERE id = @id";
db.Execute(query, new { id = userId });

// El parámetro @id es tratado como dato, no código
```

**Por qué:** Prepared statements separan la estructura SQL de los datos, imposibilitando que el input se interprete como comando.

### Ataque Típico

```
Input: ' OR '1'='1
Query ejecutada: SELECT * FROM users WHERE username = '' OR '1'='1'
Resultado: Devuelve todos los usuarios (1=1 siempre es true)
```

## Cómo Prevenirla

| Técnica | Efectividad | Descripción |
|---------|-------------|-------------|
| Prepared Statements | ⭐⭐⭐ | Métodos parametrizados de ORM/driver |
| Input Validation | ⭐⭐ | Validar longitud, tipo, formato (no es suficiente) |
| Sanitización | ⭐⭐ | Escapar caracteres especiales (menos seguro) |
| Least Privilege | ⭐⭐⭐ | DB user con permisos mínimos |
| WAF (Web Application Firewall) | ⭐⭐ | Capa de protección externa |

> [!warning] Validación NO es suficiente
> No confíes únicamente en validación client-side.
> Un atacante puede bypasear JavaScript.
> **Siempre usa prepared statements server-side.**

> [!tip] Mejor Práctica
> Usa un ORM (Entity Framework, Hibernate, Spring Data JPA).
> Los ORMs usan prepared statements automáticamente.
> Reduce la surface de ataque significativamente.

## Referencia

- [[MOC - DevSecOps]] — Mapa completo de prácticas de desarrollo seguro
- [[OWASP Top 10]] — Vulnerabilidades más críticas en aplicaciones web
- [[Guía - Prevención SQL Injection]] — Pasos prácticos para implementar defensas

#seguridad #inyección #sql #web #owasp
```

---

## CASOS DE USO COMUNES

### Caso 1: Agregar un Nuevo Concepto

1. Crear archivo en `40/Conceptos/` con nombre `[Concepto].md`
2. Iniciar con frontmatter YAML (type: concepto)
3. Escribir definición + contexto + ejemplos ❌/✅
4. Terminar con Referencia + tags
5. Enlazarlo en su MOC correspondiente

### Caso 2: Crear un Nuevo Proyecto

1. Crear carpeta en `20/PROYECTOS/[PROYECTO] - [Nombre]/`
2. Crear archivo principal `[PROYECTO] - [Nombre].md` (epicentro)
3. Crear subcarpetas si es necesario (Tareas, Guías, Notas)
4. Enlazar desde ÁREA madre (si viene de una área)
5. Enlazar Conceptos y Guías de 40-RECURSOS en su contenido

### Caso 3: Detectar y Resolver un Duplicado

1. Identificar archivos con nombres similares en carpetas diferentes
2. Crear nota `[REFACTOR] - Consolidar [Tema]` en 00-META
3. Decidir cuál es "fuente de verdad" (usualmente 40-RECURSOS si es teórica)
4. Cambiar enlaces en otras notas para que apunten a la fuente de verdad
5. Mover o eliminar el duplicado
6. Marcar REFACTOR como completada

### Caso 4: Encontrar un Enlace Roto

1. Ejecutar reporte de backlinks en Obsidian
2. Identificar enlace sin destino
3. O bien: crear el archivo destino, O bien: reemplazar el enlace con uno válido
4. Verificar que la nota destino tiene sección Referencia con backlink inverso

---

## CHECKLIST DE CALIDAD

Antes de considerar una nota "completa", verifica:

- [ ] **Frontmatter:** ¿Tiene type, state, created, updated, tags?
- [ ] **Títulos:** ¿El H1 (`# Título`) coincide con el nombre del archivo?
- [ ] **Contenido:** ¿Tiene definición/introducción clara en el primer párrafo?
- [ ] **Ejemplos ❌/✅:** ¿Hay al menos un ejemplo comparativo si es aplicable?
- [ ] **Callouts:** ¿Son útiles o están de relleno? (Solo si aportan valor)
- [ ] **Tabla de Referencia:** ¿Tiene mínimo 2 enlaces relacionados?
- [ ] **Enlaces Validados:** ¿Todos los `[[wikilinks]]` apuntan a archivos existentes?
- [ ] **Tags:** ¿Tiene etiquetas semánticas (máximo 5)?
- [ ] **Sin Duplicados:** ¿Este nombre no existe en otra carpeta?
- [ ] **Cierre:** ¿Termina con sección Referencia y tags?

---

## REGLAS ESPECIALES POR CONTEXTO

### Para Notas Técnicas (Código, DevOps, etc.)

- Siempre incluye ❌/✅ con ejemplos reales
- Especifica el lenguaje de programación en bloques ` ``` `
- Incluye por qué cada ejemplo funciona o falla

### Para Notas Conceptuales (Teoría, Principios)

- Incluye contexto histórico si es relevante
- Cita fuentes (links a recursos externos o notas relacionadas)
- Explica aplicación práctica más allá de la teoría

### Para Guías (How-Tos)

- Numeración clara de pasos (1., 2., 3., etc.)
- Prerequisitos explícitos al inicio
- Diagrama ASCII de flujo si el proceso es complejo

### Para MOCs

- Introducción máximo 100 palabras
- Índice categorizado (no es una lista plana)
- Actualizar cada vez que se agreguen notas nuevas al dominio

---

## ALIASES: CUÁNDO Y CÓMO USARLOS

Usa aliases en frontmatter cuando una nota se conoce por múltiples nombres:

```yaml
aliases: [Nombre Alternativo 1, Nombre Alternativo 2]
```

**Cuándo sí:**
- Acrónimos: `SQL Injection` → alias `SQLi, SQL Injection Attacks`
- Variantes de lenguaje: `DevSecOps` → alias `Desarrollo Seguro, Dev-Sec-Ops`
- Nombres comunes vs técnicos: `Inyección SQL` → alias `SQL Injection`

**Cuándo no:**
- Abreviaturas personales (confunden)
- Typos comunes (no acostumbres mala ortografía)

---

## RESUMEN: FLUJO IDEAL PARA CREAR/EDITAR NOTAS

```
1. Revisar si INBOX tiene notas sin procesar → Clasificar
2. Decidir tipo de nota (MOC/Concepto/Guía/Proyecto/Área)
3. Verificar nombre: ¿Existe duplicado? ¿Sigue convención?
4. Crear con frontmatter completo
5. Escribir contenido siguiendo estructura (Def/Contexto/Ejemplos)
6. Si aplica: ❌/✅ comparativos
7. Si aplica: Callouts estratégicos
8. Cerrar con sección Referencia
9. Añadir tags
10. Validar todos los wikilinks existen
11. Enlazar desde MOC correspondiente
12. ¿Encontraste algo roto? → Reportar antes de editar
```

---

## REFERENCIA

- Este es un archivo de referencia para el sistema PKM
- Ubicación en el Vault: `00 - META/PROTOCOLO-PKM-VAULT.md`
- Consultar siempre antes de crear o modificar notas

#protocolo #sistema #pkm #obsidian #referencia
