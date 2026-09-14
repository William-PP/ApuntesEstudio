---
type: documento
state: activa
priority: alta
created: 2026-08-30
updated: 2026-09-10
related: [GestionDeportiva, SIMAD, DevSecOps-Proyecto]
tags: [gestion-deportiva, modelo-datos, base-datos, modelo-relacional, requisitos, seguridad]
aliases: [Modelo de Datos GestionDeportiva, Diccionario GestionDeportiva, Modelo Entidades GestionDeportiva]
---

# Modelo de Datos — GestionDeportiva

> [!info] Contexto
> Nota consolidada del modelo de datos del **módulo deportivo** de SIMAD. Reúne las dos capas que coexisten en el proyecto:
> (1) el **modelo conceptual de 5 niveles** (compartido con SIMAD) y
> (2) el **modelo técnico v4** (TPT sobre `Persona`) que el repo `GestionDeportiva` implementa.
> El detalle completo de requisitos y el diccionario ampliado viven en el proyecto hermano → [[20 - PROYECTOS/SIMAD/SIMAD]].

---

## 1. Capas del modelo

| Capa | Alcance | Dónde vive el detalle |
|------|---------|------------------------|
| **Conceptual (5 niveles)** | Identidad, Acceso, Estructura deportiva, Eventos/Competencias, Gestión continua | [[20 - PROYECTOS/SIMAD/Diccionario de Entidades - SIMAD]] · [[20 - PROYECTOS/SIMAD/Etapa 1/Narrativa - SIMAD]] |
| **Técnico v4** | Modelo relacional implementado (SQL Server, TPT) | [[20 - PROYECTOS/DevSecOps-Proyecto/GestionDeportiva-Diccionario-Datos]] · [[20 - PROYECTOS/DevSecOps-Proyecto/GestionDeportiva-Diagrama-Clases]] · `docs/GestionDeportiva-Documentacion-Completa.md` del repo |

> [!warning] Divergencia de motores
> El modelo v4 está documentado para **SQL Server**. El **README actual del repo declara `SQLModel + SQLite`** como persistencia del backend en desarrollo. La arquitectura es igualmente por capas; el motor concreto debe confirmarse en la Entrega 2.

---

## 2. Modelo conceptual — 5 niveles (compartido con SIMAD)

| Nivel | Funcionalidad | Entidades principales |
|-------|---------------|-----------------------|
| **1 — Identidad y Roles** | Registro maestro de personas | `Persona`, `Entrenador`, `Deportista`, `Usuario` |
| **2 — Acceso y Permisos** | Visibilidad según rol | `Rol`, `Permiso`, `RolPermiso` |
| **3 — Estructura Deportiva** | Equipos y su historial | `Equipo`, `PersonaEquipo` |
| **4 — Eventos y Competencias** | Torneos, modalidades, categorías, inscripciones, resultados | `Torneo`, `Modalidad`, `Categoria`, `Inscripcion`, `Resultado` |
| **5 — Gestión Continua** | Bienestar, salud y entrenamiento | `Reporte_Bienestar`, `Novedad_Medica`, `Rutina_Tecnico`, `Asistencia_Entrenamiento` |

> [!tip] Alcance real del repo (README → distribución de tareas)
> - **Dev 1:** Persona, Entrenador, Deportista, **Equipo**
> - **Dev 2:** Modalidad, Categoria, Torneo, TorneoModalidad, TorneoCategoria
> - **Dev 3:** Inscripcion, Resultado, **NovedadMedica, RutinaTecnico, AsistenciaEntrenamiento, ReporteBienestar**
>
> Confirma que el alcance abarca el modelo v4 (torneos) **más** las entidades de gestión continua del nivel 5 del modelo conceptual.

---

## 3. Modelo técnico v4 — resumen

Patrón **TPT (Table-per-Type)** sobre `Persona`: `Entrenador` y `Deportista` comparten PK (`numero_documento`) con `Persona`; una persona puede ser ambas cosas a la vez.

```
Persona (base, PK numero_documento)
├── Entrenador  (PK/FK → Persona)
├── Deportista  (PK/FK → Persona)
│
EntrenadorDeportista — histórico temporal (fecha_inicio / fecha_fin / estado)
Torneo ── TorneoModalidad ── Modalidad
   │        └────────────── Categoria (vía TorneoCategoria)
   └─ Inscripcion (1..*)
        └─ Resultado (1:0..1)
Auditoria — genérica, polimórfica (sin FK real)
```

### Entidades técnicas

| # | Entidad | Rol en el modelo | Nota clave |
|---|---------|------------------|-----------|
| 1 | `Persona` | Base TPT | PK varchar(20) → pasaportes/CE alfanuméricos |
| 2 | `Entrenador` | Subtipo TPT | Comparte PK con Persona |
| 3 | `Deportista` | Subtipo TPT | Comparte PK con Persona; datos de salud (`tipo_sangre`, `lesiones`) |
| 4 | `EntrenadorDeportista` | Histórica | PK surrogate; exige trigger anti-solapamiento de períodos |
| 5 | `Torneo` | Evento principal | Estado por check: BORRADOR → … → CANCELADO/SUSPENDIDO |
| 6 | `Modalidad` | Catálogo | Individual \| Parejas \| Equipo |
| 7 | `Categoria` | Catálogo | div_edad, div_peso, sexo, nivel |
| 8 | `TorneoModalidad` | Asociativa | PK propia (la referencia Inscripcion) |
| 9 | `TorneoCategoria` | Asociativa | PK propia (la referencia Inscripcion) |
| 10 | `Inscripcion` | Acto previo al torneo | UQ real sin `id_entrenador` (evita doble inscripción si es NULL) |
| 11 | `Resultado` | Posterior al torneo | Relación 1:0..1 con Inscripcion (UQ) |
| 12 | `Auditoria` | Genérica | Sin FK real; `id_registro` = PK auditada como texto |

### Gaps de integridad conocidos (documentados en el repo)

- `EntrenadorDeportista` no valida solapamiento de períodos (falta trigger).
- `Inscripcion` trae dos FKs a Torneo sin garantizar que apunten al mismo torneo (gap `TorneoModalidad` vs `TorneoCategoria`).
- `Auditoria` guarda PII en texto plano (`numero_documento`, etc.) — revisar confidencialidad.
- PK por documento de identidad se propaga como FK a todo el modelo — la API **no debe** exponer `numero_documento` crudo.

---

## 4. Seguridad del modelo (mapeo OWASP Top 10 2025)

| Campo / Entidad | Categoría | OWASP 2025 | Mitigación |
|---|---|---|---|
| `tipo_sangre`, `lesiones` | Dato de salud | A04 Cryptographic Failures | Cifrado en reposo (column-level o a nivel de app) |
| `direccion`, `telefono`, `fecha_nacimiento` | Dato personal (Ley 1581) | A04 Cryptographic Failures | Cifrado en reposo; enmascarar en logs |
| `email` | Dato personal | A04 Cryptographic Failures | No usarlo como identificador público; rate limiting |
| `numero_documento` | PK sensible (FK propagada) | A04 Cryptographic Failures | Nunca exponerlo crudo en respuestas ni logs |
| `Resultado.medalla` | Integridad de datos | A08 Software or Data Integrity Failures | Audit log; rol Organizador/Admin al escribir |
| Endpoints de inscripción | Abuso / spam | A01 Broken Access Control | Autorización por rol + rate limiting |

---

## 5. Requisitos del sistema

### 5.1 Requisitos de usuario

| ID | Rol | Capacidades clave |
|----|-----|-------------------|
| RU-001 | Deportista | Auto-reporte de bienestar, historial médico, asistencias, solicitud de equipo, resultados |
| RU-002 | Entrenador | Registrar competencias, ver atletas (bienestar/salud/asistencias/resultados), rutinas, asignaciones |
| RU-003 | Monitor | Vistas limitadas: inscripciones, asistencias, resultados, crear torneos (sin historial médico ni rutinas) |
| RU-004 | Administrador | Gestión de usuarios/roles/permisos/configuración, backups, auditoría completa |

### 5.2 Requisitos funcionales (núcleo)

| Área | RF clave | Regla asociada |
|------|----------|----------------|
| Torneos | RF-001 Crear Torneo · RF-002/RF-003 Configurar Modalidad/Categoría | RN-004 (definir modalidad+categoría antes de inscribir) |
| Inscripciones | RF-004/RF-005 Registro · RF-006/RF-016 Validación de restricción | RN-001 (bloquear sin "Apto para competir") · RN-010 (medalla automática) |
| Resultados | RF-007/RF-008/RF-009 Registrar y visualizar | RN-015 (inmutabilidad tras 48h) |
| Bienestar | RF-010/RF-011/RF-012 Formulario diario + recordatorio + historial | — |
| Salud | RF-013/RF-014/RF-015/RF-017/RF-018 Novedades y restricciones | RN-002 (privacidad médica) · RN-013 (no borrar restricciones resueltas) |
| Equipos | RF-025/RF-026/RF-027 Agregar/solicitar/notificar | RN-005 (flujo aceptar/rechazar) |
| Perfil | RF-028/RF-029 Auto-gestión y visibilidad por rol | RN-012 (datos oficiales solo Admin) |

> [!note] Detalle completo de requisitos
> Tablas completas RU / RF / RNF / RN en [[20 - PROYECTOS/SIMAD/Etapa 1/Narrativa - SIMAD]] (documento compartido).

---

## Referencia

- [[20 - PROYECTOS/GestionDeportiva/GestionDeportiva]] — epicentro del proyecto
- [[20 - PROYECTOS/GestionDeportiva/Guías - GestionDeportiva]] — cómo se implementa este modelo (arquitectura + setup)
- [[20 - PROYECTOS/DevSecOps-Proyecto/GestionDeportiva-Diccionario-Datos]] — DDL detallado del modelo v4
- [[20 - PROYECTOS/DevSecOps-Proyecto/GestionDeportiva-Diagrama-Clases]] — UML v4 + análisis de seguridad OWASP
- [[20 - PROYECTOS/SIMAD/Diccionario de Entidades - SIMAD]] — diccionario conceptual ampliado (5 niveles)
- [[20 - PROYECTOS/SIMAD/Etapa 1/Narrativa - SIMAD]] — requisitos completos compartidos

#gestion-deportiva #modelo-datos #diccionario #requisitos #seguridad #simad