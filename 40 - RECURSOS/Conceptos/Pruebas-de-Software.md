---
type: concepto
state: activa
priority: alta
created: 2026-09-04
updated: 2026-09-04
related: [MOC - DevSecOps, Testing, MOC - CI-CD]
tags: [testing, istqb, calidad, qa, ieee, cmmi, sqa, pruebas]
aliases: [Pruebas de Software, Test de Software, QA, Software Quality Testing]
---

# Pruebas de Software

## QUÉ es

La gestión de pruebas de software establece las **fases, el plan de trabajo y la organización general** de las acciones del equipo asignado. Las pruebas son un componente estratégico en la creación de software: permiten detectar fallas, oportunidades de mejora y realizar ajustes en las fases iniciales del desarrollo, evitando costos elevados de corrección o fallas críticas en producción.

Los objetivos del proceso de pruebas:

| Objetivo | Descripción |
|----------|-------------|
| Mejorar tiempos de desarrollo | Detectar problemas temprano, antes de que escalen |
| Asegurar calidad entregada | No entregar fallas al usuario final |
| Evitar reprocesos | Reducir la pérdida económica de rehacer trabajo |

> [!tip] Independencia del equipo
> Para asegurar la mayor eficacia posible (alta probabilidad de *encontrar* fallas), las pruebas deben realizarlas personas independientes de quienes desarrollaron el software.

---

## Ciclo de Gestión de Pruebas

La gestión de pruebas contempla un ciclo de **cuatro fases** principales:

| Fase | Qué se hace |
|------|-------------|
| **Planificación** | Estrategia general y alcance de las pruebas |
| **Diseño** | Análisis de la documentación: técnicas apropiadas, condiciones de prueba, elementos a evaluar y pasos detallados de ejecución |
| **Ejecución** | Ejecutar según el plan y los roles asignados (requiere datos de prueba y ambientes listos). Se documenta y reporta cada error o hallazgo |
| **Validación y control de cambios** | Re-pruebas tras los ajustes para validar soluciones y certificar que las correcciones no rompieron otras funcionalidades |

```
┌────────────────────────────────────────────────────────────┐
│                 Ciclo de Gestión de Pruebas                │
│                                                            │
│  ┌─────────────┐    ┌──────────────┐    ┌───────────────┐  │
│  │ Planificación│───▶│    Diseño     │───▶│   Ejecución   │  │
│  └─────────────┘    └──────────────┘    └───────┬───────┘  │
│                                                 │          │
│                                                 ▼          │
│  ┌──────────────────────────────────────────────────────┐  │
│  │      Validación y control de cambios                 │  │
│  │  (re-pruebas, certificación de correcciones)         │  │
│  └──────────────────────────────────────────────────────┘  │
└────────────────────────────────────────────────────────────┘
```

---

## Planificación de Pruebas y el "Plan de Pruebas"

La fase de planificación tiene como entregable principal el **Plan de Pruebas**: documento que describe el alcance según el software específico, sus objetivos, el enfoque de evaluación y el análisis de impacto si ocurrieran fallas.

### Proceso (3 etapas secuenciales)

1. **Conocimiento del software** — Lectura profunda de la documentación e interacción directa con el sistema para comprender su objetivo.
2. **Identificación de necesidades** — Listado de ítems y funcionalidades específicas que requieren ser probadas.
3. **Elaboración del Plan de Pruebas**.

### Estructura del documento

| Sección del Plan de Pruebas | Contenido |
|-----------------------------|-----------|
| Descripción del software | Finalidad, características, destinatarios, dispositivos compatibles |
| Condiciones de pruebas | Necesidades del espacio y orientaciones generales |
| Objetivos de las pruebas | Expectativas generales y específicas |
| Elementos requeridos | Ambientes: servidores, SO, herramientas de software |
| Lista de ítems a probar | Funcionalidades, módulos y roles de usuario a validar |
| Lista de ítems que NO deben probarse | Módulos fuera de alcance (para optimizar esfuerzo) |
| Entregables | Casos de prueba, scripts, reportes |
| Roles y responsabilidades | Funciones de cada integrante |
| Plan de trabajo general | Cronograma con fechas límite — recomendado **Diagrama de Gantt** (actividades, duraciones, recursos, dependencias) |
| Riesgos e instrucciones de suspensión | Factores de retraso (ej. datos de prueba defectuosos) y pautas para detener temporalmente el proceso ante irregularidades críticas |

> [!warning] Riesgo típico
> Los **datos de prueba defectuosos** son un factor común que retrasa el cronograma. Definir instrucciones de suspensión permite detener el proceso de forma controlada ante irregularidades críticas.

---

## Certificación Profesional: ISTQB

El **ISTQB** (*International Software Testing Qualifications Board*) regula la certificación internacional para ingenieros de software que realizan actividades de pruebas.

| Dato | Detalle |
|------|---------|
| Creación | 2002, por organizaciones y especialistas del área |
| Misión | Promover las pruebas como profesión: unificar buenas prácticas, investigación y criterios homogéneos de evaluación |

### Ruta multinivel (3 grados)

| Nivel | Nombre |
|-------|--------|
| 1 | **Básico** (*Foundations*) |
| 2 | **Avanzado** |
| 3 | **Experto** |

### Perfiles de certificación

- Líder de pruebas
- Analista de pruebas
- Analista de pruebas técnicas
- Gerencia de pruebas
- Mejora continua del proceso de pruebas
- Automatización de pruebas *(en elaboración en el momento del documento)*
- Pruebas de seguridad *(en elaboración en el momento del documento)*

---

## Estándares Internacionales de Pruebas

> [!info] Rol de los estándares
> Las normas técnicas unifican estrategias, procedimientos, nomenclaturas y estructuras documentales a nivel global. Aumentan la productividad del equipo y facilitan la comunicación cliente-proveedor.

### IEEE-829 (1983) — Documentación de pruebas

Diseñado para documentar las pruebas de software. Define **formatos estándar** para cada etapa (no limita qué documentos usar):

| Documento | Contenido |
|-----------|-----------|
| *Plan de pruebas* | Alcance, recursos y enfoque |
| *Especificación del diseño* | Casos de prueba, resultados esperados, criterios de aceptación |
| *Especificación de casos de prueba* | Datos precisos requeridos para el inicio |
| *Procedimientos de prueba* | Paso a paso detallado y requisitos previos |
| *Informe de elementos de prueba* | Bitácora cronológica con resultados de ejecuciones |
| *Reporte de incidentes* | Evidencias e impactos de comportamientos anormales (errores o defectos) |
| *Informe de resumen* | Compilación de resultados generales y recomendaciones |
| *Registro de pruebas* | Control del estado de avance y observaciones |

### IEEE-1008 (1987) — Pruebas unitarias

Define normas para ejecutar **pruebas unitarias** a través de 3 fases que engloban 8 actividades estratégicas:

1. **Planeación de la prueba**
2. **Adquisición del conjunto de pruebas**
3. **Medidas de unidades de prueba**

### CMMI

Modelo de **integración de madurez y calidad** de software. Valida la calidad interna y externa del producto mediante el uso integrado de **métricas de software**.

### Plan SQA (Aseguramiento de la Calidad de Software)

Herramienta de calidad orientada a **auditar el desarrollo** para garantizar la aplicación estricta de las normas de prueba. Define:

| Componente | Qué cubre |
|------------|-----------|
| Gestión | Organización del aseguramiento de calidad |
| Tareas | Actividades de verificación y validación |
| Estándares mínimos de diseño | Normas que debe cumplir el diseño de software |
| Planes de verificación/validación | Cómo se comprueba que el software cumple lo esperado |
| Gestión de riesgos | Identificación y control de riesgos del proceso |

### Comparativa de estándares

| Estándar | Año | Enfocado en | Entregable principal |
|----------|-----|-------------|----------------------|
| IEEE-829 | 1983 | Documentación de pruebas | Formatos estándar de documentos |
| IEEE-1008 | 1987 | Pruebas unitarias | 3 fases / 8 actividades |
| CMMI | — | Madurez y calidad del proceso | Métricas de software integradas |
| SQA | — | Auditoría del desarrollo | Plan de aseguramiento de calidad |

---

## Relación con DevSecOps

Las pruebas son prerequisito del pipeline DevSecOps: la fase de testing automatizado (unit + integration) habilita las etapas de SAST/SCA dentro del CI/CD. La independencia del equipo y la documentación estandarizada (IEEE-829) se conectan directamente con los requisitos de [[Testing]] en el proyecto [[Flujo de un proyecto]].

---

## Referencia

- [[MOC - DevSecOps]] — ruta de estudio; el testing es Nivel 3 prerequisito para CI/CD
- [[MOC - CI-CD]] — donde las pruebas automatizadas se ejecutan en el pipeline
- [[SAST]] — análisis estático, complementa las pruebas dinámicas
- [[Testing]] — nota de testing automatizado del proyecto

#testing #istqb #calidad #qa #ieee #cmmi #sqa #pruebas