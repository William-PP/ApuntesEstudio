---
type: documento
state: activa
priority: alta
created: 2026-09-05
updated: 2026-09-05
related: [SIMAD]
tags: [simad, stack, decisiones-tecnicas, arquitectura, azure, seguridad]
aliases: [Stack Tecnológico SIMAD, Decisiones Técnicas SIMAD]
---

# Stack Tecnológico — SIMAD

> [!info] Etapa 1 — Definición y Planificación Inicial
> Decisiones técnicas iniciales del **Sistema Integral de Gestión Deportiva (SIMAD)**: lenguaje, framework, base de datos, cloud, autenticación y herramientas de seguridad. Hace parte del entregable de la **Etapa 1** según la [[Guía - Documentación de Proyectos de Software]].

---

## Stack Tecnológico

### Vista Consolidada

| Capa | Componente | Tecnología | Justificación |
|---|---|---|---|
| Backend | Lenguaje | C# | Garantiza eficiencia, robustez y un tipado estricto ideal para arquitecturas empresariales y escalables. |
| Backend | Framework | .NET 10 | Selección estratégica debido a su soporte de larga duración (LTS), asegurando estabilidad, alto rendimiento y soporte hasta 2028. |
| Base de Datos | Sistema | SQL Server | Sistema robusto y altamente cohesivo con el ecosistema Microsoft, ideal para gestionar transacciones complejas y datos relacionales. |
| Base de Datos | ORM / Acceso a Datos | Entity Framework Core y Dapper | EF Core gestiona con agilidad las operaciones transaccionales y el modelo de dominio, mientras que Dapper aporta velocidad superior en consultas masivas de alto rendimiento. |
| Frontend | Framework UI | React | Estándar de la industria basado en componentes, altamente modular y perfecto para arquitecturas desacopladas. |
| Frontend | Lenguaje | TypeScript | Aporta tipado estricto e interfaces, ofreciendo una transición natural desde C# y previniendo errores en tiempo de desarrollo. |
| Despliegue | Contenedores | Docker | Permite empaquetar la aplicación y sus dependencias en contenedores ligeros, asegurando un entorno idéntico y reproducible desde el desarrollo hasta producción. Al menos por ahora está fuera del alcance debido a que dicha aplicación se piensa desplegar como serverless. |
| Despliegue | Infraestructura | Azure | Ofrece alta compatibilidad nativa con el ecosistema .NET, además de herramientas avanzadas de resiliencia, respaldos y automatización DevOps. |
| Automatización | CI/CD | GitHub Actions | Integración nativa con el control de versiones en GitHub, ofreciendo flujos automatizados potentes y gratuitos para compilación, pruebas y despliegue. |
| Seguridad | SAST | SonarCloud | Analiza la calidad estática del código y detecta vulnerabilidades lógicas directamente en las Pull Requests, operando en la nube con baja fricción. |
| Seguridad | SCA | Trivy y Snyk | Trivy aporta velocidad extrema y escaneo de paquetes y contenedores, mientras que Snyk complementa con un rastreo profundo de dependencias y sugerencias de remediación. |
| Seguridad | Secrets Detection | Gitleaks | Bloquea la filtración de credenciales y tokens de forma preventiva tanto localmente (pre-commit hooks) como en el pipeline de CI/CD. |
| Seguridad | DAST | OWASP ZAP | Valida de forma dinámica y externa la seguridad de la Web API en un entorno de pruebas (staging), simulando ataques reales. |
| Monitoreo | Stack LGTM | Prometheus + Grafana + Loki | Stack completo que unifica métricas de rendimiento, registros estructurados (logs) y trazabilidad distribuida para un diagnóstico en tiempo real. |
| Calidad | Testing | Pendiente | Fase de definición en curso para la implementación de pruebas unitarias y de integración (xUnit). |
| Calidad | Documentación API | Swagger | Se selecciona como la herramienta estándar de documentación porque se genera de forma automática directamente desde el código, garantizando que los contratos nunca se desactualicen y ofreciendo una interfaz interactiva para el frontend en React. |

### Detalle

#### Lenguaje de Programación
- **Backend**: C#
  - Justificación: Garantiza eficiencia, robustez y un tipado estricto ideal para arquitecturas empresariales y escalables.

### Framework Principal
- **Backend**: .NET 10
  - Justificación: Selección estratégica debido a su soporte de larga duración (LTS), asegurando estabilidad, alto rendimiento y soporte hasta 2028.

### Base de Datos
- **Tecnología**: SQL Server
  - Justificación: Sistema robusto y altamente cohesivo con el ecosistema Microsoft, ideal para gestionar transacciones complejas y datos relacionales.

### ORM / Acceso a Datos
- **Entity Framework Core** y **Dapper**
  - EF Core gestiona con agilidad las operaciones transaccionales y el modelo de dominio
  - Dapper aporta velocidad superior en consultas masivas de alto rendimiento

### Frontend
- **Framework UI**: React
  - Justificación: Estándar de la industria basado en componentes, altamente modular y perfecto para arquitecturas desacopladas.
- **Lenguaje**: TypeScript
  - Justificación: Aporta tipado estricto e interfaces, ofreciendo una transición natural desde C# y previniendo errores en tiempo de desarrollo.

---

## Cloud vs On-Premise

### Decisión: **Nube - Azure (Serverless)**

#### Justificación:
- Alta compatibilidad nativa con el ecosistema .NET
- Herramientas avanzadas de resiliencia, respaldos y automatización DevOps
- Manejo y configuración del servidor adaptable a múltiples necesidades

#### Arquitectura Específica: **Azure Functions (Serverless)**

**Razones de selección:**
- Escalabilidad horizontal para manejar múltiples instancias según demanda
- Eficiencia de costos mensuales (pago por consumo)
- Manejo de concurrencia de usuarios variable según día y hora
- Capa gratuita disponible

**Nota**: Despliegue como serverless, no Docker (fuera del alcance por ahora)

---

## Autenticación

### Sistema de Autenticación
- **Tipo**: Basado en Roles (RBAC)
- **Método**: Contraseñas hasheadas + JWT (según política de secretos)
- **Cumplimiento**: Ley 1581 (Protección de Datos Personales)

### Roles Principales Configurados (5 roles base):
1. **Deportista**
2. **Entrenador**
3. **Monitor**
4. **Metodólogo**
5. **Root** (Administrador)

### Controles de Acceso:
- Autenticación segura con credenciales hasheadas
- Autorización basada en roles granulares
- Cada usuario solo ve información autorizada (privacidad de datos médicos)
- 2FA para usuarios administrativos

---

## Herramientas de Seguridad y Calidad

### CI/CD
- **Herramienta**: GitHub Actions
  - Justificación: Integración nativa con control de versiones en GitHub, flujos automatizados potentes y gratuitos

### Análisis de Código (SAST)
- **Herramienta**: SonarCloud
  - Detecta vulnerabilidades lógicas en Pull Requests

### Análisis de Dependencias (SCA)
- **Herramientas**: Trivy y Snyk
  - Trivy: Escaneo rápido de paquetes y contenedores
  - Snyk: Rastreo profundo de dependencias y sugerencias de remediación

### Detección de Secretos
- **Herramienta**: Gitleaks
  - Bloquea filtración de credenciales en pre-commit hooks y pipeline de CI/CD

### Análisis Dinámico de Seguridad (DAST)
- **Herramienta**: OWASP ZAP
  - Valida seguridad de Web API en ambiente staging

### Monitoreo y Observabilidad
- **Stack**: Prometheus + Grafana + Loki (LGTM completo)
  - Métricas de rendimiento
  - Registros estructurados (logs)
  - Trazabilidad distribuida

### Documentación API
- **Herramienta**: Swagger
  - Generación automática desde código
  - Interfaz interactiva para frontend React
  - Garantiza contratos actualizados

### Testing
- **Estado**: Pendiente definición
- **Tecnología planeada**: xUnit (pruebas unitarias e integración)

---

## Contenedores (Nota)

### Docker
- **Estado**: Fuera del alcance por ahora
- **Razón**: Arquitectura Serverless (Azure Functions) no requiere contenedores tradicionales
- **Consideración futura**: Posible uso en fases posteriores si cambia el enfoque de despliegue

---

## Control de Versiones
- **Plataforma**: GitHub
- **Integración**: Nativa con GitHub Actions y SonarCloud

---

## Resumen de Decisiones Estratégicas

| Componente | Decisión                               | Justificación Clave                                |     |
| ---------- | -------------------------------------- | -------------------------------------------------- | --- |
| Backend    | C# + .NET 10                           | Robustez, tipado estricto, LTS hasta 2028          |     |
| BD         | SQL Server                             | Transacciones complejas, ecosistema Microsoft      |     |
| Frontend   | React + TypeScript                     | Componentes modulares, transición natural desde C# |     |
| Cloud      | Azure Serverless                       | Escalabilidad variable, eficiencia de costos       |     |
| CI/CD      | GitHub Actions                         | Integración nativa, gratuito                       |     |
| Seguridad  | Multi-capa (SAST, SCA, DAST, secretos) | Cumplimiento Ley 1581                              |     |
| Monitoreo  | Prometheus + Grafana + Loki            | Observabilidad completa en tiempo real             |     |

---

## Referencia

- [[SIMAD]] — epicentro del proyecto
- [[Acta de Constitución - SIMAD]] — definición del proyecto (Etapa 1)
- [[Seguridad Inicial - SIMAD]] — amenazas y secretos que validan estas decisiones (Etapa 1)
- [[Guía - Documentación de Proyectos de Software]] — plantilla de la Etapa 1 aplicada

#simad #stack #decisiones-tecnicas #arquitectura #azure #seguridad #etapa-1