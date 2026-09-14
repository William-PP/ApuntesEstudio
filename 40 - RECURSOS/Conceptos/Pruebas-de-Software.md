---
type: concepto
state: activa
priority: alta
created: 2026-09-04
updated: 2026-09-05
related: [MOC - DevSecOps, Testing, MOC - CI-CD]
tags: [testing, istqb, calidad, qa, ieee, cmmi, sqa, pruebas, tecnicas, ejecucion, herramientas]
aliases: [Pruebas de Software, Test de Software, QA, Software Quality Testing, Diseño de Pruebas]
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

## Tipos de Pruebas

El software se divide conceptualmente en una parte **interna** (lógica y programación) y otra **externa** (interfaz y experiencia de usuario). A partir de esto se definen los siguientes tipos de pruebas:

| Tipo | Qué valida | Alcance |
|------|-----------|---------|
| **Pruebas Funcionales** | Que el sistema cumpla los requerimientos iniciales acordados con el cliente | Comportamiento externo de la interfaz, flujo de eventos según el rol del usuario, datos ingresados y de salida, e interoperabilidad (comunicación) entre sistemas |
| **Pruebas No Funcionales** | Aspectos técnicos y de calidad del software | Rendimiento (carga, estrés, tensión, volumen), seguridad, confiabilidad, instalación/configuración, almacenamiento, documentación y planes de respaldo/recuperación |

> [!example] Caso práctico
> Análisis de **estrés** sobre la plataforma SOFIA Plus para evaluar su tolerancia a la concurrencia de usuarios.

## Estrategia Escalada de Pruebas

Para garantizar el éxito, la evaluación dinámica del sistema debe ir **de lo menor a lo mayor** en cuatro pasos secuenciales:

```
┌──────────────┐   ┌────────────────┐   ┌───────────────┐   ┌────────────────┐
│  Unitarias   │──▶│  Integración   │──▶│   Sistema     │──▶│  Aceptación    │
│  (módulo)    │   │  (módulos +    │   │ (software +   │   │ (certificación │
│              │   │   interfaces)  │   │   hardware)   │   │  del cliente)  │
└──────────────┘   └────────────────┘   └───────────────┘   └────────────────┘
```

| Paso | Qué evalúa |
|------|-----------|
| **Pruebas unitarias** | De forma aislada, cada módulo individual del código |
| **Pruebas de integración** | Cómo se combinan los módulos y cómo operan sus interfaces de comunicación |
| **Pruebas de sistema** | Que el software totalmente ensamblado con su hardware cumpla los requisitos definidos |
| **Pruebas de aceptación** | Que el cliente final certifique que el sistema funciona bajo sus expectativas reales |

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

## Técnicas de Diseño de Pruebas

Las técnicas se agrupan en **cuatro familias** complementarias:

| Familia | Acceso que requiere | Enfoque |
|---------|--------------------|---------|
| **Caja Negra** | Ninguno al código | Comportamiento externo: entradas → salidas |
| **Caja Blanca** | Código fuente + lógica de programación | Estructura interna del código |
| **Estáticas** | Documentación y código (revisión manual/asistida) | Detección de fallas antes de ejecutar |
| **Basadas en la Experiencia** | Intuición del evaluador | Lecciones aprendidas de ingenieros expertos |

### A. Caja Negra (pruebas funcionales externas)

No requieren acceso ni conocimiento de la programación interna. Se enfocan en el **procesamiento de entradas y salidas** de datos.

| Sub-técnica | Qué hace |
|-------------|----------|
| **Análisis de valor límite** | Prueba los valores máximos y mínimos permitidos |
| **Partición de equivalencias** | Agrupa los datos en categorías para comprobar que produzcan resultados iguales |
| **Combinación de parámetros** | Cruza combinaciones de valores de entrada |
| **Transiciones de cambios de estado** | Verifica el comportamiento ante cambios de estado del sistema |
| **Rutas del software** | Modela caminos sobre el flujo principal y flujos alternativos |

> [!example] Caso práctico
> Se utiliza la funcionalidad **"Registrar datos básicos aspirante"** de SOFIA Plus para modelar caminos basados en su flujo principal y sus flujos alternativos.

### B. Caja Blanca (pruebas de estructura interna)

Requieren acceso al **código fuente** y conocimientos técnicos de su lógica. Evalúan la estructura interna.

| Sub-técnica | Qué hace |
|-------------|----------|
| **Pruebas de bucles** | Análisis de la estructura y complejidad de las iteraciones |
| **Caminos independientes** | Identifica rutas lógicas mediante un **diagrama de grafos** (nodos = acciones, arcos = enlaces, regiones delimitadas) para verificar los distintos caminos de ejecución |
| **Mutaciones** | Modificación deliberada del código para evaluar si el set de pruebas actual detecta la falla |
| **Pruebas de condición y de flujo de datos** | Validan decisiones lógicas y el recorrido de los datos |

### C. Técnicas estáticas

Revisiones **manuales o asistidas por herramientas** para identificar causas de fallas antes de la ejecución formal. Sirven de complemento a las de caja negra y blanca.

| Técnica | Propósito |
|---------|-----------|
| **Management Review** | Toma de decisiones sobre la dirección del proyecto |
| **Technical Review** | Cumplimiento técnico bajo planes y estándares |
| **Software Inspection** | Revisión del cumplimiento de especificaciones y normas del software |
| **Walkthrough** | Análisis guiado para detectar errores u omisiones tempranas |
| **Auditoría** | Certificación global del proceso y del producto final |

### D. Pruebas basadas en la experiencia

Aprovechan la **intuición de ingenieros expertos** bajo lecciones aprendidas:

| Técnica | Qué es |
|---------|--------|
| **Ad Hoc** | Basadas puramente en la intuición del evaluador |
| **Exploratorias** | Se diseñan y modifican de manera dinámica mientras se navega en el software |

---

## Diseño de Casos de Prueba

Un **Caso de Prueba** es el documento que formaliza los **datos de entrada, las condiciones operativas y el resultado esperado** para determinar si el software funciona según lo requerido.

### Elementos obligatorios

| Elemento | Qué contiene |
|----------|--------------|
| **Nombre e Identificador único** | Código descriptivo del caso de prueba |
| **Descripción y función a probar** | Qué valida y en qué módulo o unidad se sitúa |
| **Condiciones iniciales** | Requisitos previos o datos necesarios para arrancar la prueba |
| **Flujo** | Secuencia numerada de pasos necesarios para la ejecución |
| **Resultado esperado** | Lo que debería ocurrir teóricamente si el sistema funciona bien |
| **Resultado obtenido** | Comportamiento real del sistema al realizar la prueba |
| **Estado** | Condición actual: Pendiente, realizada, satisfactoria, fallida |
| **Configuración requerida** | Ambiente informático de hardware, software o datos de prueba necesarios |
| **Nombre de quien ejecutó** | Ejecutor de la prueba |

> [!example] Caso práctico
> Aplicación real de esta plantilla evaluando el escenario del **"intento de registro con un usuario previamente registrado"** dentro del módulo de admisiones de SOFIA Plus.

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

## Gestión y Control de la Ejecución de Pruebas

La ejecución de pruebas requiere obligatoriamente una **planeación previa** de su alcance, el equipo responsable y el tiempo asignado. Durante este proceso, los casos de prueba diseñados previamente se convierten en los **documentos operativos** que se deben diligenciar en la práctica.

### Alistamiento de Pruebas

Antes de iniciar, el equipo debe asegurar la disponibilidad y configuración de los siguientes elementos estratégicos:

| Elemento | Qué incluye |
|----------|-------------|
| **Datos de prueba** | Preparar y configurar en el sistema todos los datos que se ingresarán o visualizarán (generados, guardados o aleatorios — estos últimos ideales para pruebas de carga) |
| **Software** | Configurar servidores, máquinas virtuales, servidores web, simuladores e instancias de bases de datos exclusivas para pruebas |
| **Herramientas de soporte** | Aplicaciones informáticas que asistirán el proceso, listas y configuradas |
| **Hardware** | Componentes físicos necesarios: disco duro, procesador, memoria y dispositivos multimedia |
| **Cronograma** | Detallar las ejecuciones basándose en los casos de prueba identificados y las técnicas seleccionadas |
| **Equipo de personas** | Personal que domine técnicamente el software y conozca los requerimientos funcionales del negocio, para evitar sesgos por expectativas ajenas al alcance inicial |

> [!example] Caso práctico
> El equipo que validó el sistema **SOFIA Plus del SENA** requirió dominio experto en los procesos académicos de la institución.

### Recomendaciones y Ciclo de Vida de los Errores

- **Pruebas exploratorias previas**: navegación inicial para que el equipo se familiarice con la interfaz antes de la ejecución sistemática.
- **Segunda validación**: re-evaluar los errores detectados cuando la funcionalidad afectada se reutilice en múltiples secciones del software.
- **Cierre del ciclo**: inicia desde la primera versión y puede requerir múltiples iteraciones. Un ciclo se cierra formalmente cuando se ejecutan todas las pruebas planeadas y se solucionan las no conformidades.

**Clasificación de No Conformidades** (jerárquica):

| Tipo | Descripción |
|------|-------------|
| **Fatales** | Errores críticos que bloquean el sistema y no permiten continuar la prueba |
| **Mejoras** | Propuestas de optimización no contempladas inicialmente en los requerimientos |
| **Funcionales** | Desviaciones donde no se obtiene el resultado esperado en las validaciones |
| **Visualización** | Errores estéticos, ortográficos, de alineación o de incumplimiento de estándares de diagramación |

---

## Documentación de la Ejecución

El registro formal de los resultados es indispensable para **certificar el estado del sistema** y orientar los ajustes. Dos reportes esenciales:

### Informe de Incidente de Pruebas

Recopila de forma redactada y precisa **cada fallo detectado**:

| Dato | Contenido |
|------|-----------|
| **Encabezado** | Fecha de la prueba y nombre del evaluador |
| **Caso de prueba** | Identificador, nombre y estado (pasó, falló o no se ejecutó) |
| **Ubicación del incidente** | Unidad, módulo, API o funcionalidad |
| **Pasos ("Cómo")** | Secuencia detallada para replicar el error |
| **Condiciones** | Datos de entrada y dispositivos de hardware utilizados |
| **Hallazgo** | Descripción del error acompañado de una evidencia visual (imagen o captura) |
| **Resultado esperado** | El comportamiento que el sistema debió tener teóricamente |

### Resumen de Pruebas

Reporte de gestión que consolida **métricas clave** para la toma de decisiones: porcentaje de avance, porcentaje de no conformidades detectadas y porcentaje de pruebas aceptadas.

---

## Herramientas de Soporte

Programas que permiten **automatizar y agilizar** tareas dentro del proceso de pruebas. Se clasifican en tres tipos:

| Tipo | Función |
|------|---------|
| **Gestión de pruebas** | Organizar actividades, asignar responsabilidades, registrar incidentes, trazar avances y generar gráficos gerenciales |
| **Pruebas funcionales** | Registrar casos de prueba y simular eventos de usuario para verificar flujos funcionales del sistema |
| **Pruebas de carga y rendimiento** | Simular accesos masivos y uso concurrente para medir tiempos de respuesta y la capacidad límite de servidores e infraestructura |

### Ejemplos de herramientas: uso libre vs comercial

| Categoría | Uso Libre (gratuitas) | Comerciales (pagas) |
|-----------|----------------------|---------------------|
| **Gestión** | Bugzilla Testopia, qaManager, qaBook, RTH, Salome-tmf, Squash TM, TestLink, Testitool, Data Generator | HP Quality Center/ALM, QA Complete, qaBook, PractiTest, SpiraTest, TestLog, Zephyr |
| **Funcionales** | Selenium, SoapUI, Watir, WatiN, Canoo Webtest, WET, WebInject | QuickTest Pro, Rational Robot, Sahi, SoapTest, Test Complete, QA Wizard, Squish |
| **Carga** | Funkload, FWPTT, loadUI, JMeter | HP LoadRunner, LoadStorm, Neo Load, WebLOAD Professional, Load Impact |

### Herramientas destacadas

| Herramienta | Licencia | Qué hace |
|-------------|----------|----------|
| **Bugzilla / Testopia** | Gratuita | Gestión y seguimiento de errores: prioridades, estimación de tiempos, responsables, evidencias y notificaciones por correo. Testopia administra además los casos de prueba |
| **Generatedata** | Gratuita | PHP/JS/MySQL; genera aleatoriamente grandes volúmenes de datos (hasta miles de filas) en HTML, Excel, XML, CSV o SQL |
| **SoapTest** | Comercial | Pruebas de integración para APIs, Web Services y entornos en la nube |
| **WebInject** | Gratuita | Perl/XML; automatiza pruebas de servicios web y entornos de navegación |
| **WebLoad** | Comercial | JavaScript; simula acceso concurrente y genera reportes analíticos de rendimiento de BD, servidores y sitios web |
| **JMeter** | Gratuita | Carga y rendimiento: volumen de hilos de usuario, hora exacta de ejecución y respuestas mediante gráficos |
| **QA Complete** | Comercial | Plataforma que unifica gestión de casos, entornos, defectos, automatizaciones y planeación |
| **Testitool / qaBook** | Gratuita / mixta | Testitool (PHP) crea e instancia planes de prueba; qaBook ofrece versiones gratuitas y comerciales |
| **WET** | Gratuita | Automatiza interacciones web (clics en botones, ingreso de campos, accesos de enlaces) |
| **Test Studio** | Comercial | Cubre todo el espectro: automatización de interfaces, componentes, integraciones, pruebas móviles y de escritorio + gestión del ciclo completo |

---

## Relación con DevSecOps

Las pruebas son prerequisito del pipeline DevSecOps: la fase de testing automatizado (unit + integration) habilita las etapas de SAST/SCA dentro del CI/CD. La independencia del equipo y la documentación estandarizada (IEEE-829) se conectan directamente con los requisitos de [[Testing]] en el proyecto [[Flujo de un proyecto]].

---

## Referencia

- [[MOC - DevSecOps]] — ruta de estudio; el testing es Nivel 3 prerequisito para CI/CD
- [[MOC - CI-CD]] — donde las pruebas automatizadas se ejecutan en el pipeline
- [[MOC - Pruebas de Software]] — índice temático de testing y QA
- [[SAST]] — análisis estático, complementa las pruebas dinámicas
- [[Testing]] — nota de testing automatizado del proyecto

#testing #istqb #calidad #qa #ieee #cmmi #sqa #pruebas #tecnicas #caja-negra #caja-blanca #casos-de-prueba #ejecucion #incidentes #resumen-de-pruebas #herramientas