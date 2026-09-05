---
type: guía
state: activa
priority: alta
created: 2026-09-05
updated: 2026-09-05
related: [MOC - DevSecOps, Pruebas-de-Software, Git-y-GitHub]
tags: [guia, proyecto, documentacion, ciclo-de-vida, planificacion, despliegue, monitoreo]
aliases: [Handbook Documentación, Guía de Proyectos Software, Documentación Mínima Proyecto]
---

# Guía - Documentación de Proyectos de Software

> [!info] Handbook genérico
> Plantilla de documentación mínima para **cualquier proyecto de programación**, cubriendo las 8 etapas del ciclo de vida: definición → viabilidad → diseño → planificación → ejecución → control → pruebas → despliegue. Genérica y reutilizable en cualquier proyecto del vault ([[GestionDeportiva]], [[SIMAD]], [[Flujo de un proyecto]], etc.).

## Tabla de Contenidos

1. [Etapa 1: Definición y Planificación Inicial](#etapa-1-definición-y-planificación-inicial)
2. [Etapa 2: Análisis de Viabilidad](#etapa-2-análisis-de-viabilidad)
3. [Etapa 3: Diseño y Arquitectura](#etapa-3-diseño-y-arquitectura)
4. [Etapa 4: Planificación Detallada](#etapa-4-planificación-detallada)
5. [Etapa 5: Ejecución](#etapa-5-ejecución)
6. [Etapa 6: Control y Monitoreo](#etapa-6-control-y-monitoreo)
7. [Etapa 7: Pruebas y Validación](#etapa-7-pruebas-y-validación)
8. [Etapa 8: Despliegue y Cierre](#etapa-8-despliegue-y-cierre)

---

# ETAPA 1: DEFINICIÓN Y PLANIFICACIÓN INICIAL

**Objetivo:** Definir qué vamos a construir, por qué, y validar que sea viable.

## Qué Hacer

### Documentación Mínima

- **Acta de Constitución del Proyecto (Project Charter):** nombre, descripción breve, objetivos, usuarios finales
- **Narrativa verbal**: Descripción del Problema Actual, Causas Identificadas, Impacto en el Negocio y los Usuarios, Flujo Actual de Procesos, Propuesta de Solución, Beneficios Operacionales, 
- **Alcance (Scope):** qué entra, qué no entra
- **Riesgos iniciales:** riesgos principales (técnicos, seguridad, operacionales)

### Decisiones Técnicas Iniciales

**Stack Tecnológico**

- Lenguaje de programación (Python, Node.js, Java, Go, etc.)
- Framework principal (Django, Express, Spring Boot, etc.)
- Base de datos: SQL o NoSQL (PostgreSQL, MongoDB, etc.)
- Cloud vs on-premise (AWS, Azure, GCP, VPS, etc.)
- Autenticación: OAuth, JWT, sesiones, etc.

### Seguridad Inicial

- Clasificar datos: ¿Procesamos PII, datos financieros, etc.?
- Regulaciones: ¿GDPR, CCPA, PCI-DSS, locales, ninguna?
- Threat landscape: ¿Quién querría atacar esto?
- Definir política de secretos: nunca hardcodear credenciales

### Equipo & Comunicación

- Definir roles: PM, tech lead, security (mínimo)
- Cadencia de reuniones: retrospectiva + planning semanal
- Stakeholders: quién necesita updates
- Criterio de éxito: ¿cuándo consideramos "listo"?

## Entregables

- [ ] Acta de constitucion del Proyecto: Informacion basica del proyecto, justificacion y problema, objetivos del proyecto, usuarios finales y grupos de interes, alcance del proyecto, restricciones y proyectos, matriz de interesados, exito del proyecto, riesgos identificados
- [ ] Narrativa verbal: Descripción del Problema Actual, Causas Identificadas, Impacto en el Negocio y los Usuarios, Flujo Actual de Procesos, Propuesta de Solución, Beneficios Operacionales, 
- [ ] Stack Tecnologico: Lenguaje, frameworks, base de datos, etc.
- [ ] Seguridad inicial: Clasificacion de datos, regulaciones, threat landscapes y politicas de secretos.

---

# ETAPA 2: ANÁLISIS DE VIABILIDAD

**Objetivo:** Validar que el proyecto es técnicamente posible y definir requisitos claros.

## Qué Hacer

### Especificación de Requisitos

**Requisitos Funcionales (RF)**

- Casos de uso principales (máximo 5-10)
- Flujos críticos y casos de uso (cómo el usuario interactúa)
- APIs principales (endpoints que necesitamos)
- Integraciones externas (si aplica)

**Requisitos No Funcionales (RNF)**

- Performance: ¿cuántos usuarios simultáneos?
- Disponibilidad: ¿99%, 99.9%, 99.99%?
- Escalabilidad: ¿crecimiento esperado?
- Seguridad: requisitos de encriptación, etc.

### Análisis de Viabilidad Técnica

- ¿Es posible con el stack elegido?
- ¿Hay librerías/frameworks disponibles?
- ¿Qué habilidades del equipo necesitamos?
- ¿Qué riesgos técnicos identificamos?
- ¿Cuál es el tiempo estimado?

### Threat Modeling

**Threat Modeling (STRIDE)**

- Identificación de amenazas por componente
- Clasificación por tipo (Spoofing, Tampering, etc.)

**Análisis de Attack Surface**

- Puntos de entrada de datos
- Interfaces de usuario
- APIs
- Integraciones con terceros
- Base de datos
- Sistema de ficheros

**Matriz de Riesgos de Seguridad**

- Amenaza, Vulnerabilidad, Impacto
- Severidad (Crítica, Alta, Media, Baja)
- Probabilidad (Alta, Media, Baja)

**Requisitos de Seguridad Derivados de Amenazas**

- Autenticación (MFA, 2FA, SSO)
- Autorización (RBAC, ABAC)
- Encriptación (datos en tránsito, en reposo)
- Auditoría (qué se loguea)
- Validación de entrada (allowlist vs blacklist)

**Análisis de Cumplimiento Normativo**

- Regulaciones identificadas (GDPR, CCPA, PCI-DSS, HIPAA, etc.)
- Requisitos específicos por regulación
- Gaps vs requisitos del proyecto
- Timeline para cumplimiento

**Clasificación de Datos Detallada**

| Clasificación | Significado |
|---------------|-------------|
| Público | Sin restricción de acceso |
| Interno | Solo empleados |
| Confidencial | Acceso limitado (clientes VIP, finanzas) |
| Restringido | Acceso muy limitado (credenciales, PHI) |

**Matriz de Permisos/Roles Inicial**

- Roles esperados (admin, user, viewer, etc.)
- Permisos por rol
- Cadena de autorización

**Preguntas por cada dato sensible**

- ¿Dónde se almacena? (BD, caché, logs)
- ¿Quién puede acceder?
- ¿Cómo se transmite? (HTTP, WebSocket, etc.)
- ¿Cómo se protege? (encriptación, permisos, etc.)

### Arquitectura Conceptual

- Diagrama básico: frontend - backend - BD
- Flujo de datos: cómo fluye la información
- Componentes principales: lista de servicios/módulos
- Dependencias externas: qué servicios externos necesitamos

### Setup Inicial de Repositorio

- Crear repositorio Git (GitHub, GitLab)
- Estructura base de carpetas
- `.gitignore` configurado
- Rama main protegida (no push directo)
- Rama dev para cambios

## Entregables

- [ ] Especificación de requisitos (RF + RNF)
- [ ] Análisis de viabilidad técnica
- [ ] Threat modeling (por cada dato sensible)
- [ ] Arquitectura conceptual (diagrama + descripción)
- [ ] Repositorio Git configurado
- [ ] Estimación inicial de tiempo (en sprints/semanas)

---

# ETAPA 3: DISEÑO Y ARQUITECTURA

**Objetivo:** Diseñar en detalle cómo construiremos la solución.

## Qué Hacer

### Diseño de Arquitectura Detallado

**Diagrama de Clases (UML)**

- Clases del dominio
- Atributos
- Métodos
- Relaciones (herencia, composición, asociación)
- Interfaces

**Componentes de Arquitectura**

- Arquitectura de capas: presentación, lógica, datos
- Patrones de diseño: MVC, CQRS, etc.
- Componentes: qué componentes específicos necesitamos
- Interfaces entre componentes: cómo se comunican
- Decisiones técnicas: por qué elegimos esto vs aquello (**ADR**)

### Diseño de Base de Datos

- Schema: tablas/colecciones y relaciones
- Índices: campos que necesitan indexarse
- Constraints: reglas de integridad
- Migraciones: cómo actualizaremos el schema
- Backup strategy: cómo protegemos los datos

### Diseño de APIs

- Endpoints principales: lista de rutas
- Request/Response: formato de datos (JSON, etc.)
- Autenticación: cómo validamos requests
- Versionado: ¿v1, v2, etc.?
- Documentación: OpenAPI/Swagger spec

### Diseño de Seguridad

**Plan de Hardening**

- Configuraciones de seguridad por defecto
- Desactivar features innecesarias
- Cambiar defaults (puertos, usuarios, etc.)
- Parches y updates

**Controles de Seguridad**

- Autenticación: OAuth2, JWT, sesiones, etc.
- Autorización: roles y permisos (admin, user, etc.)
- Encriptación: datos en tránsito (HTTPS) y en reposo
- Validación de inputs: cómo sanitizamos datos
- Secrets management: cómo guardamos credenciales
- Logging de seguridad: qué eventos logueamos

### Design System (UI/UX)

- Color palette: colores principales
- Tipografía: fuentes, tamaños
- Componentes: botones, inputs, etc.
- Layouts: cómo estructuramos pantallas
- Wireframes: sketches de páginas principales

### Infraestructura

- Entornos: desarrollo, staging, producción
- Serverless vs containers: Docker, Kubernetes, Lambda, etc.
- Bases de datos: dónde se alojan
- CDN: si usamos cachés
- Load balancing: cómo distribuimos tráfico

## Entregables

- [ ] Arquitectura detallada (diagrama + documento)
- [ ] Schema de BD (SQL o data model)
- [ ] API specification (OpenAPI/Swagger)
- [ ] Security design document
- [ ] Design System (colores, tipografía, componentes)
- [ ] Infrastructure architecture (diagrama)
- [ ] ADRs: decisiones técnicas documentadas

> [!note] Siguiente etapa
> Una vez el diseño esté aprobado → pasar a Etapa 4.

---

# ETAPA 4: PLANIFICACIÓN DETALLADA

**Objetivo:** Desglosar el trabajo en sprints/tareas ejecutables.

## Qué Hacer

### Work Breakdown Structure (WBS)

- Desglosar requisitos en features
- Cada feature en tasks técnicas (backend, frontend, BD, etc.)
- Estimar: cuánto tiempo toma cada task (horas/días)
- Identificar dependencias: qué debe hacerse primero

### Planificación de Sprints

- Agrupar tasks en sprints (1-2 semanas cada uno)
- Definir qué se entrega cada sprint (MVP first, después features)
- Prioridad: MVP crítico → features importantes → nice-to-have
- Velocidad del equipo: cuánto pueden hacer por semana

### CI/CD Pipeline Setup

- Definir etapas: lint → test → build → deploy
- Code quality gates: SonarQube, Checkmarx
- Dependency scanning: Snyk, Dependabot
- Automated testing en cada PR
- Staging environment antes de prod

### Testing Strategy

**Tipos de Testing**

- Unit tests: cada función/componente
- Integration tests: componentes juntos
- E2E tests: flujos de usuario completos
- Performance tests: carga y stress testing
- Security tests: SAST, SCA, DAST

### Git Workflow

- Feature branches: git flow o trunk-based
- Code review: mínimo 2 aprobaciones
- Commit messages: conventional commits
- Merge strategy: squash o merge commits
- Tag strategy: semantic versioning

## Entregables

- [ ] Work Breakdown Structure (WBS) completo
- [ ] Plan de sprints (4-8 semanas)
- [ ] CI/CD pipeline definida
- [ ] Testing strategy y herramientas
- [ ] Git workflow documentado
- [ ] Estimaciones de capacidad del equipo

---

# ETAPA 5: EJECUCIÓN

**Objetivo:** Construir la solución según plan.

## Qué Hacer

### Desarrollo del Código

- Seguir style guide del equipo
- Commits atómicos y descriptivos
- Code coverage mínimo 70%
- Documentación inline en código complejo
- README con setup instructions

### Code Review Process

- Cada PR revisada antes de mergear
- Mínimo 2 aprobaciones
- Checklist: funcionalidad, tests, seguridad, performance
- Feedback constructivo y educativo

### Documentación

- Architecture Decision Records (ADRs)
- API documentation (Swagger/OpenAPI)
- Setup guide para nuevos desarrolladores
- Runbooks para operaciones

### Quality Gates

- Lint: código limpio (ESLint, Pylint, etc.)
- Tests: cobertura > 70%, todos pasan
- Security: no hardcoded secrets, vulnerabilities check
- Performance: baseline establecida
- Build: sin warnings

## Entregables

- [ ] Código en repositorio (main branch protegida)
- [ ] Todos los tests unitarios pasan
- [ ] Coverage > 70%
- [ ] API documentada
- [ ] No hay secretos en código
- [ ] MVP funcional en staging
- [ ] Documentación actualizada

---

# ETAPA 6: CONTROL Y MONITOREO

**Objetivo:** Preparar infraestructura y monitoreo para producción.

## Qué Hacer

### Monitoreo

**Métricas a Monitorear**

- Application: latencia, error rate, throughput
- Infrastructure: CPU, memoria, disco, red
- Database: conexiones, queries lentos, locks
- Business: usuarios activos, transacciones, conversiones

**Dashboards**

- Overview: salud general del sistema
- Alerts: incidentes activos
- Performance: tendencias de latencia y errors
- Business: KPIs importantes

### Logging

- Logging centralizado (ELK, Splunk, CloudWatch)
- Niveles: DEBUG, INFO, WARNING, ERROR, CRITICAL
- Estructurado: JSON con context fields
- Retention: definir políticas de almacenamiento
- Seguridad: **no loguear PII o credenciales**

### Alertas

| Severidad | Acción |
|-----------|--------|
| **Critical** | Page on-call inmediatamente |
| **High** | Notificación pero no urgente |
| **Medium** | Ticket para investigar |
| **Low** | Log para análisis futuro |

**Umbrales de Alertas**

- Error rate > 1%
- Latencia p95 > baseline + 50%
- CPU/Memory > 80%
- Disk > 85%
- Requests queued > threshold

### On-Call & Runbooks

**On-Call Rotation**

- Escala de 1-2 semanas por persona
- Contacto directo y claro
- Escalation procedure
- Handoff meeting

**Runbooks (para cada alerta)**

- Síntoma: qué significa la alerta
- Diagnóstico: cómo investigar
- Resolución: pasos para arreglarlo
- Escalation: cuándo pedir ayuda

### Disaster Recovery

- **RTO** (Recovery Time Objective): cuánto tarda recuperar
- **RPO** (Recovery Point Objective): cuántos datos pierdo
- Failover: si servidor principal cae, qué pasa
- Backup strategy: qué, cuándo, dónde
- Restore testing: verificar backups regularmente

## Entregables

- [ ] Monitoreo configurado (métricas + dashboards)
- [ ] Logging centralizado
- [ ] Alertas configuradas
- [ ] On-call plan definido
- [ ] Runbooks documentados
- [ ] Backup automatizado y testeado

---

# ETAPA 7: PRUEBAS Y VALIDACIÓN

**Objetivo:** Validar que el sistema funciona antes de producción.

## Qué Hacer

### Testing Final

**Tests Unitarios (desarrolladores)**

- Cada función tiene tests
- Casos normales + edge cases + errores
- Cobertura mínimo 70%
- Ejecutados en CI automáticamente

**Tests de Integración (developers + QA)**

- APIs funcionan correctamente
- BD se actualiza como esperado
- Servicios externos se integran bien
- Flujos completos funcionan end-to-end

**Tests E2E (QA)**

- 3-5 flujos críticos de usuario
- Pruebas en staging (ambiente idéntico a prod)
- Herramientas: Cypress, Selenium, Playwright
- Ejemplo: login → crear recurso → compartir → logout

**Tests de Performance (QA + DevOps)**

- Load testing: ¿aguanta 100 usuarios simultáneos?
- Stress testing: ¿qué pasa a 1000 usuarios?
- Herramientas: JMeter, K6, Gatling
- Identificar bottlenecks

**Security Testing**

- SAST (Static): SonarQube, CheckMarx en CI
- SCA (Dependencies): Snyk, Dependabot en CI
- DAST (Dynamic): ZAP, Burp Suite manual

**Security Checklist**

- No hay credenciales en código
- Inputs están validados
- Outputs están escapados
- Autenticación funciona
- Autorización funciona
- HTTPS obligatorio
- CORS configurado correctamente
- Rate limiting activado

**Penetration Testing (si presupuesto permite)**

- Contratar profesional externo
- 1-2 semanas antes de deploy
- Encontrar vulnerabilidades reales
- Plan de remediar antes de producción

### Validación Funcional

- Acceptance criteria: cada requisito se cumple
- Casos de uso: cada caso de uso funciona
- UI/UX: interfaz es usable
- Performance: respuestas en tiempo aceptable
- Datos: información se muestra correctamente

### Sign-off

- PM: requisitos cumplidos
- QA: tests pasan, no hay bugs críticos
- Security: no hay vulnerabilidades críticas
- DevOps: infraestructura lista
- Stakeholders: aprueban el lanzamiento

### Preparación para Producción

- Database migration script escrito y testeado
- Secrets en vault/secrets manager (no en código)
- Certificates SSL válidos
- CDN configurado (si aplica)
- Backups automatizados
- On-call setup: quién responde a qué hora
- Runbook: pasos exactos para deployar
- Rollback plan: cómo volver a versión anterior
- Communication plan: qué decir a usuarios

## Entregables

- [ ] Todos los tests pasan
- [ ] Cobertura de código > 70%
- [ ] No hay vulnerabilidades críticas
- [ ] Performance benchmarks aceptables
- [ ] Security penetration testing completado
- [ ] Sign-off de todos los stakeholders
- [ ] Runbook escrito y probado
- [ ] Plan de rollback definido

---

# ETAPA 8: DESPLIEGUE Y CIERRE

**Objetivo:** Llevar a producción y estabilizar el sistema.

## Qué Hacer

### Pre-Deployment (T-1 día)

**Checklist Final**

- [ ] Todos los tests pasan en CI
- [ ] Code review completado
- [ ] No hay secretos en código
- [ ] BD migrada en staging, funciona
- [ ] Monitoreo activo
- [ ] Alertas configuradas
- [ ] Backups testeados
- [ ] Runbook listo
- [ ] Team informado: quién hace qué
- [ ] Release notes escritas
- [ ] Comunicación a usuarios lista

### Plan de Despliegue

**Timeline del Deployment**

```
T+0:00   - Pre-flight checks (infra, monitoreo, alertas)
T+0:15   - Deploy a producción (blue-green o canary)
         (blue-green: 2 ambientes idénticos, switch instantáneo)
         (canary: 5% tráfico → 50% → 100%)
T+0:30   - Smoke tests en producción
T+1:00   - Comunicación a usuarios: "está en vivo"
T+2:00   - Monitoreo intensivo: equipo alerta
T+4:00   - Reducir monitoreo si todo bien
T+24:00  - Retrospectiva: qué salió bien, qué mejorar
```

### During Deployment

**Equipo y Responsabilidades**

- Alguien en CI/CD: ejecuta deployment
- Alguien en Monitoreo: observa métricas/logs
- Alguien en Comms: comunica status
- Tech lead: decisiones rápidas si hay problemas
- Contacto de seguridad: por si hay incidente de seguridad

### Post-Deployment (primeras horas)

**Métricas Críticas a Monitorear**

- Error rate: debe estar < 1% (vs baseline)
- Response time: p95 debe estar dentro de esperado
- CPU/Memory: debe estar < 80%
- BD conexiones: debe estar normal
- Usuarios reportando issues: recolectar bugs

### Si Algo Falla

**Decisión (5-10 minutos)**

- Hotfix: si es pequeño, hacerlo en vivo
- Rollback: si es grave, volver a versión anterior

**Procedimiento de Rollback**

1. Identificar el problema
2. Decidir rollback
3. Revertir a última versión estable (< 5 min)
4. Comunicar a usuarios
5. Analizar root cause
6. Plan de fix: qué haremos diferente

### Después del Deploy (1-2 semanas)

**Soporte Post-Lanzamiento**

- On-call 24/7 (primeros 3-7 días)
- Responder bugs rápido
- Recolectar feedback de usuarios
- Hotfixes si es necesario
- Comunicación diaria: cómo va

**Métricas a Trackear**

- Estabilidad: ¿cuántos bugs?
- Performance: ¿responde rápido?
- Usuario feedback: ¿les gusta?
- Adopción: ¿cuántos usan?

### Cierre del Proyecto

**1-2 semanas después del deploy**

**Retrospectiva**

- Equipo de desarrollo
- PM, DevOps, Security, QA
- Duración: 1-2 horas

**Documentación Final**

- Arquitectura del sistema (estado actual)
- Operación: cómo mantener
- Escalabilidad: qué optimizar después
- Lecciones aprendidas

**Transferencia a Operaciones**

- Entrenar team de ops: cómo operar
- Documentar runbooks finales
- Setup on-call permanente
- Monitoreo entregado

**Archivo del Proyecto**

- Documentación en repo
- ADRs documentados
- Roadmap de mejoras futuras
- Formal closure: proyecto "done"

## Entregables

- [ ] Deployment exitoso a producción
- [ ] Sistema estable y monitorizado
- [ ] Release notes publicadas
- [ ] Usuarios informados
- [ ] Retrospectiva completada
- [ ] Documentación final
- [ ] Handoff a equipo de operaciones

---

# RESUMEN Y HERRAMIENTAS

## Resumen: Qué Hacer en Cada Etapa

| Etapa | Duración | Entrada | Actividades | Salida |
|-------|----------|---------|-------------|--------|
| **1. Definición** | 1-2 sem | Idea del negocio | Charter, scope, stack tech | Proyecto aprobado |
| **2. Viabilidad** | 1-2 sem | Proyecto aprobado | RF/RNF, threat model, arch conceptual | Diseño validado |
| **3. Diseño** | 1-2 sem | Validación | Arquitectura, DB schema, APIs, security design | Diseño detallado |
| **4. Planificación** | 1 sem | Diseño | WBS, sprints, CI/CD, testing plan | Plan de ejecución |
| **5. Ejecución** | 4-8 sem | Plan | Código, tests, PRs, documentación | MVP en staging |
| **6. Monitoreo** | Paralelo a 5 | Desarrollo | Monitoreo, logging, alertas, DR | Infra lista |
| **7. Pruebas** | 1-2 sem | MVP listo | Testing final, security, validación | Listo para prod |
| **8. Despliegue** | 1-2 sem | Validado | Deploy, soporte post-lanzamiento, cierre | Sistema en producción |

```
┌──────┐  ┌──────────┐  ┌────────┐  ┌──────────────┐
│ 1.Definición │→│ 2.Viabilidad│→│ 3.Diseño │→│ 4.Planificación │
└──────┘  └──────────┘  └────────┘  └──────────────┘
                                        │
                                        ▼
┌──────┐  ┌────────────┐  ┌────────┐  ┌──────────────┐
│ 8.Despliegue │←│ 7.Pruebas │←│ 6.Control │←│ 5.Ejecución │
└──────┘  └────────────┘  └────────┘  └──────────────┘
```

## Herramientas Recomendadas por Categoría

| Categoría | Opciones |
|-----------|----------|
| **VCS** | GitHub, GitLab, Bitbucket |
| **CI/CD** | GitHub Actions, GitLab CI, Jenkins, Azure Pipelines |
| **Secretos** | Vault, AWS Secrets Manager, Azure Key Vault |
| **Monitoreo** | Prometheus + Grafana, Datadog, New Relic, CloudWatch |
| **Logging** | ELK Stack, Splunk, CloudWatch Logs |
| **Testing** | pytest, Jest, JUnit, Cypress, Selenium, JMeter |
| **SAST** | SonarQube, Checkmarx (gratuita para código abierto) |
| **SCA** | Snyk, Dependabot, OWASP Dependency-Check |
| **DAST** | OWASP ZAP, Burp Suite Community |
| **Container** | Docker, Docker Compose, Kubernetes (después) |
| **Infraestructura** | Terraform, CloudFormation, AWS/Azure/GCP consola |

---

## Referencia

- [[MOC - DevSecOps]] — ruta de estudio que materializa estas etapas en el pipeline
- [[Pruebas-de-Software]] — fundamentos del ciclo de pruebas (Etapas 4 y 7)
- [[Git-y-GitHub]] — base de control de versiones y workflow (Etapas 2-5)
- [[Flujo de un proyecto]] — ejemplo aplicado del ciclo de vida en el vault
- [[GestionDeportiva]] — proyecto real donde esta guía se aplica

**Versión:** 1.0 · **Uso interno:** referencia para gestión de proyectos pequeños

#guia #proyecto #documentacion #ciclo-de-vida #planificacion #despliegue #monitoreo