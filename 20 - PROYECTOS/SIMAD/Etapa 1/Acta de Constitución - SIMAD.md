---
type: documento
state: activa
priority: alta
created: 2026-09-05
updated: 2026-09-05
related: [SIMAD]
tags: [documentacion, simad, proyecto, charter, alcance, riesgos]
aliases: [Charter SIMAD, Acta de Constitución SIMAD, Project Charter SIMAD]
---

# Acta de Constitución — SIMAD

> [!info] Etapa 1 — Definición y Planificación Inicial
> Charter del **Sistema Integral de Gestión Deportiva (SIMAD)**. Define qué se construye, por qué, para quién, alcance, riesgos iniciales y criterios de éxito. Hace parte del entregable de la **Etapa 1** según la [[Guía - Documentación de Proyectos de Software]].

---

## 1. Información Básica del Proyecto

### 1.1 Nombre del Proyecto

Sistema Integral de Gestión Deportiva (SIMAD)

Módulo deportivo centralizado para la gestión integral de deportistas, entrenadores, competencias y datos de bienestar.

### 1.2 Descripción Breve

Sistema de información que centraliza toda la gestión deportiva, permitiendo a entrenadores registrar competencias, monitorear el estado de salud y bienestar de los deportistas, mantener un historial completo y transferible de información, y asegurar que las restricciones médicas sean respetadas en todo momento. El sistema opera sobre una arquitectura de base de datos robusta con 5 niveles funcionales: Identidad, Acceso y Permisos, Estructura Deportiva, Eventos y Competencias, y Gestión Continua.

### 1.3 Duración Estimada del Proyecto

**Plazo: 6 a 12 meses**

- Fase 1 (Meses 1-3): Diseño, arquitectura y desarrollo del backend
- Fase 2 (Meses 4-6): Desarrollo del frontend, integración de APIs
- Fase 3 (Meses 7-9): Testing, refinamiento, capacitación
- Fase 4 (Meses 10-12): Despliegue piloto, ajustes, rollout completo

---

## 2. Justificación y Problema

### 2.1 Problema Identificado

No se cuenta actualmente con un sistema centralizado para gestionar información deportiva. Los datos están dispersos en:

- Hojas de cálculo desactualizadas
- Registros en papel
- Memorias locales de entrenadores
- Correos electrónicos desorganizados

### 2.2 Impacto del Problema

**Para entrenadores:** Pérdida de tiempo en búsqueda de información; incapacidad para seguimiento riguroso del progreso técnico

**Para deportistas:** Mayor riesgo de lesiones; entrenamientos bajo condiciones peligrosas; falta de retroalimentación estructurada

**Para la institución:** Ineficiencia operativa; riesgo legal por lesiones agravadas; pérdida de memoria institucional en transiciones de personal

### 2.3 Beneficios Esperados

- Información centralizada y de fácil acceso
- Reducción de tiempo administrativo de entrenadores (estimado 40-50%)
- Mejor prevención de lesiones mediante restricciones visibles y alertas
- Historial completo y transferible de cada deportista
- Toma de decisiones informada sobre selección y preparación de atletas
- Cumplimiento de normativas de protección de datos (Ley 1581)
- Mayor motivación de deportistas al ver su progreso documentado

---

## 3. Objetivos del Proyecto

### 3.1 Objetivo General

Desarrollar e implementar un sistema integral de gestión deportiva que centralice toda la información de deportistas, entrenadores, competencias, restricciones médicas y bienestar, permitiendo acceso seguro, oportuno y autorizado a esta información para mejorar la toma de decisiones, prevenir lesiones y asegurar la continuidad del conocimiento institucional.

### 3.2 Objetivos Específicos

**O1: Gestión Centralizada de Identidad**
- Crear una base de datos maestra única de Personas (Deportistas, Entrenadores, Administradores)
- Garantizar un registro único por documento de identidad
- Mantener información de contacto actualizada y verificable

**O2: Control de Acceso Basado en Roles**
- Implementar sistema de autenticación seguro (contraseñas hasheadas, cumplimiento Ley 1581)
- Configurar 5 roles principales: Deportista, Entrenador, Monitor, Metodólogo, Root
- Asegurar que cada usuario solo vea información autorizada (privacidad de datos médicos)

**O3: Gestión de Competencias**
- Permitir crear torneos con modalidades y categorías configurables
- Facilitar inscripción de deportistas con validación de restricciones médicas
- Registrar resultados de competencias de forma rápida y auditable

**O4: Monitoreo de Salud y Bienestar**
- Implementar sistema de auto-reporte diario de bienestar (estrés, fatiga, molestias)
- Permitir registro de restricciones médicas con estado (activa/resuelta)
- Generar alertas automáticas cuando se detecten patrones de riesgo (3+ reportes de baja motivación)

**O5: Continuidad de Información en Transiciones**
- Implementar transferencia automática de historial completo cuando un deportista cambia de entrenador
- Asegurar que nada se pierda o se borre en el proceso
- Dejar auditoría clara de quién hizo el cambio y cuándo

**O6: Historial y Trazabilidad**
- Registrar todas las operaciones importantes (inserciones, actualizaciones, eliminaciones)
- Guardar información de quién, qué y cuándo para cada cambio
- Mantener retención mínima de 1 año por cumplimiento legal

**O7: Comunicación Oportuna**
- Enviar notificaciones automáticas por correo y WhatsApp
- Alertar sobre: inscripciones confirmadas, solicitudes de equipo, restricciones críticas, recordatorios de bienestar

---

## 4. Usuarios Finales y Grupos de Interés

### 4.1 Usuarios Directos del Sistema

#### 4.1.1 Deportistas

**Cantidad estimada:** ~500-2000 usuarios activos

**Funciones principales:**
- Auto-reportar su estado diario (bienestar, molestias, fatiga)
- Inscribirse a competencias
- Consultar restricciones médicas activas
- Ver sus rutinas técnicas asignadas
- Registrar asistencia a entrenamientos
- Solicitar unirse a equipos
- Actualizar datos de contacto

**Necesidades:**
- Interfaz simple e intuitiva
- Acceso desde móvil (iOS/Android)
- Notificaciones push y recordatorios

#### 4.1.2 Entrenadores

**Cantidad estimada:** 30-50 usuarios

**Funciones principales:**
- Crear y configurar torneos (modalidades, categorías)
- Registrar resultados de competencias
- Crear rutinas técnicas y asignarlas a deportistas
- Visualizar restricciones médicas activas
- Consultar reportes de bienestar y asistencia
- Gestionar su equipo (agregar deportistas, revisar solicitudes)
- Crear restricciones médicas cuando sea necesario
- Transferir deportistas a nuevo entrenador

**Necesidades:**
- Dashboards ejecutivos con alertas
- Reportes analíticos (asistencia, progreso)
- Acceso a historial completo de cada deportista

#### 4.1.3 Administradores del Sistema

**Cantidad estimada:** 2-5 usuarios

**Funciones principales:**
- Gestionar usuarios (crear, activar, desactivar)
- Administrar roles y permisos
- Reasignar deportistas entre entrenadores
- Acceder a toda la información del sistema
- Generar reportes de auditoría
- Configurar parámetros del sistema

**Necesidades:**
- Acceso a toda la base de datos
- Herramientas de auditoría y reporte
- Panel de administración avanzado

#### 4.1.4 Monitores (Rol Especial)

**Cantidad estimada:** 30-60 usuarios

**Funciones principales:**
- Asistir a entrenadores en gestión de equipos
- Registrar asistencias y restricciones médicas
- Visualizar información de bienestar

**Necesidades:**
- Interfaz orientada a registro rápido de datos

### 4.2 Stakeholders Secundarios

#### 4.2.1 Rectoría

- **Interés:** Eficiencia operativa, cumplimiento legal, imagen institucional
- **Información clave:** Reportes ejecutivos, cumplimiento normativo

#### 4.2.2 Departamento de Bienestar Deportivo

- **Interés:** Salud de atletas, prevención de lesiones, datos para análisis
- **Información clave:** Reportes de restricciones, tendencias de bienestar

#### 4.2.3 Dirección de Tecnología

- **Interés:** Viabilidad técnica, mantenibilidad, seguridad
- **Información clave:** Arquitectura, seguridad de datos, cumplimiento de políticas de TI

#### 4.2.4 Equipo de Desarrollo (Colaboradores del Proyecto)

- **Rol:** Desarrolladores de software
- **Responsabilidad:** Diseñar, construir, probar e implementar el sistema
- **Cantidad:** 2 colaboradores principales

---

## 5. Alcance del Proyecto

### 5.1 Incluido en el Alcance

**Módulo de Identidad y Acceso:**
- Gestión centralizada de Personas (Deportistas, Entrenadores, Administradores)
- Sistema de autenticación y autorización basado en roles
- Administración de permisos granulares

**Módulo de Gestión Deportiva:**
- CRUD de Equipos y asignación de deportistas
- Creación y configuración de Torneos (modalidades y categorías)
- Inscripción de deportistas a competencias
- Registro de resultados

**Módulo de Salud y Bienestar:**
- Auto-reporte diario de bienestar (fatiga, estrés, molestias)
- Gestión de restricciones médicas (crear, resolver, visualizar)
- Rutinas técnicas (creación y asignación)
- Asistencia a entrenamientos

**Módulo de Continuidad:**
- Transferencia de historial completo entre entrenadores
- Auditoría de cambios (quién, qué, cuándo)

**Módulo de Comunicación:**
- Notificaciones automáticas por correo y WhatsApp
- Alertas de riesgo (baja motivación, restricciones críticas)

**Seguridad y Compliance:**
- Cumplimiento Ley 1581 de protección de datos

### 5.2 Excluido del Alcance

- Gestión de infraestructura física (canchas, equipamiento)
- App nativa de escritorio (solo web responsive + mobile web)
- Análisis predictivo de IA avanzada (en futuras versiones)
- Integración con wearables (pulseras inteligentes, GPS)
- Otros roles ajenos a los stakeholders principales

### 5.3 Dependencias Externas

Por definir

---

## 6. Restricciones y Supuestos

### 6.1 Restricciones

**Tecnológicas:** Debe ser compatible con navegadores modernos (Chrome, Firefox, Safari) y accesible desde móviles.

**Legales:** Cumplir Ley 1581 (protección de datos personales en Colombia).

**Operacionales:** Requiere capacitación de usuarios antes del despliegue.

### 6.2 Supuestos

- Los entrenadores adoptarán el sistema tras capacitación
- No habrá cambios legislativos que afecten los requisitos de privacidad
- Los deportistas tendrán acceso a dispositivos con internet (móvil o computadora)
- Existe un agente que autorizará el despliegue en producción

---

## 7. Matriz de Interesados

| Stakeholder | Interés | Influencia | Estrategia |
|---|---|---|---|
| Deportistas | Usabilidad, privacidad de datos | Media | Involucrar en testing, recopilar feedback |
| Entrenadores | Eficiencia, menos papeleo | Alta | Co-diseñar interfaz, capacitación intensiva |
| Metodólogo | Supervisar deportistas y entrenadores | Muy Alta | Estrategias de implementación de la aplicación |
| Equipo de desarrollo | Viabilidad técnica, mantenibilidad | Muy Alta | Autodeterminación en decisiones técnicas |
| Dirección de TI | Seguridad, mantenibilidad | Alta | Documentación, auditoría de código |
| Bienestar Deportivo | Salud atletas, prevención lesiones | Media | Acceso a reportes de bienestar |

---

## 8. Éxito del Proyecto

### 8.1 Criterios de Éxito

| Categoría | Criterio de Éxito | Métrica Objetivo |
|---|---|---|
| Funcional | Requisitos implementados | 100% de los 14 requisitos funcionales críticos implementados y testeados. |
| Rendimiento | Velocidad de respuesta | 100% de las búsquedas en menos de 2 segundos y cargas en menos de 3 segundos. |
| Adopción | Uso de usuarios | 80% o más de entrenadores usando el sistema tras 3 meses de operación. |
| Datos | Participación en registros | 90% o más de deportistas completando el auto-reporte semanal. |
| Legal | Cumplimiento normativo | Auditoría de cumplimiento de la Ley 1581 (Protección de Datos) pasada con éxito. |
| Calidad | Disponibilidad del sistema | Mínimo 95% de uptime (tiempo de actividad) en producción. |
| Seguridad | Cero vulnerabilidades | Cero brechas de seguridad identificadas durante las fases de pruebas. |

### 8.2 Indicadores Clave (KPIs)

| KPI (Indicador Clave) | Meta | Frecuencia de Medición |
|---|---|---|
| % de requisitos completados | 100% | Mensual |
| Tiempo promedio de respuesta del sistema | <2 seg | Quincenal |
| % de usuarios capacitados | 100% | Al cierre de Fase 3 |
| % de adopción de entrenadores | 80%+ | Mensual post-despliegue |
| % de reportes semanales completados | 90%+ | Semanal |
| Incidentes críticos en producción | 0 | Diario |
| % de documentación completada | 100% | Al cierre |

---

## 9. Riesgos Identificados

### 9.1 Riesgos de Alto Riesgo

| Riesgo | Probabilidad | Impacto | Mitigación |
|---|---|---|---|
| Escalabilidad insuficiente | Baja | Alto | Diseño modular de la arquitectura y realización de pruebas de carga desde el inicio del desarrollo. |
| Adopción lenta de usuarios | Media | Alto | Capacitación intensiva dirigida a los usuarios clave y provisión de soporte técnico activo post-despliegue. |

### 9.2 Riesgos de Medio Riesgo

| Riesgo | Probabilidad | Impacto | Mitigación |
|---|---|---|---|
| Cambios de requisitos | Baja | Medio | Implementación de un estricto control de cambios y despliegue temprano de un producto mínimo viable (MVP). |
| Disponibilidad de datos históricos | Media | Medio | Desarrollo de scripts de normalización y ejecución de un plan de migración manual si es necesario. |
| Integración con servicios externos (correo, SMS) | Baja | Medio | Realización de pruebas de integración tempranas con las APIs de los proveedores correspondientes. |

---

## Referencia

- [[SIMAD]] — epicentro del proyecto
- [[Stack Tecnológico - SIMAD]] — decisiones técnicas iniciales (Etapa 1)
- [[Seguridad Inicial - SIMAD]] — clasificación de datos, amenazas y secretos (Etapa 1)
- [[Guía - Documentación de Proyectos de Software]] — plantilla de la Etapa 1 aplicada

#simad #documentacion #charter #alcance #riesgos #etapa-1