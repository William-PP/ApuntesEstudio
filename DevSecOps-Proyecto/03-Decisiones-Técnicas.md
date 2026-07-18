---
tags: [proyecto/gestion-deportiva, devsecops, decisiones-tecnicas]
created: 2026-07-14
updated: 2026-07-18
---

# Decisiones Técnicas

> Este archivo explica **por qué** se eligió cada tecnología. Para ver el estado actual del stack, ir a [[DevSecOps-Proyecto/01-Arquitectura]].

## Por qué elegí cada tecnología

### Framework Backend

| Opción | Pros | Contras |
|--------|------|---------|
| .NET 10 | Consistencia y unificación de herramientas debido a Microsoft. Además, muy consistente con Azure y Docker | Dependencia total y excesiva de Microsoft |
| Spring Boot | Flexible, tiene muchas herramientas y librerías de terceros. Además, tiene un muy buen ecosistema cloud con AWS/Cloud y microservicios | Mucho consumo de RAM |

**Decisión:** .NET 10
**Justificación:** Debido al gran ecosistema de Java/Spring Boot, para mi yo actual que está aprendiendo es más fácil lidiar con una tecnología más minimalista como lo es C#/.NET 10.

### Base de Datos

| Opción | Pros | Contras |
|--------|------|---------|
| Relacionales | Consistencia fuerte | Lenta |
| No relacionales | Flexibilidad | Consistencia débil, no es inmediata |

**Decisión:** SQL Server
**Justificación:** Al ser una base de datos relacional, es perfecta para sistemas administrativos donde es necesario buscar y relacionar datos rápidamente. Además, se elige SQL Server por sobre SQLite debido a que la idea de este proyecto es practicar escenarios de concurrencia real, así como un motor servidor-cliente.

### ORM

| Opción | Pros | Contras |
|--------|------|---------|
| Entity Framework Core | Genera SQL; Full ORM | Pérdida de control del SQL generado |
| Dapper | Alta velocidad | Cero automatización |

**Decisión:** Entity Framework Core
**Justificación:** Aunque Dapper permite un mayor control del código generado y proporciona un rendimiento increíble, se eligió EF Core debido a que es un ORM que abstrae prácticamente el SQL. Para los objetivos de este proyecto, realmente es lo que más se busca.

### CI/CD

| Opción | Pros | Contras |
|--------|------|---------|
| GitHub Actions | Gratis para repos públicos y con buen límite en privados; se integra nativo con tu repo; enorme catálogo de acciones ya hechas (SonarCloud, Trivy, etc.) | Si el repo es privado, minutos limitados; menos control que self-hosted |
| GitLab CI/CD | CI/CD + registro de contenedores + seguridad integrados en una sola plataforma; buen tier gratuito | Menos ecosistema de integraciones que GitHub; comunidad más chica en LatAm |
| Jenkins | Total control, self-hosted, gratis sin límites, cualquier plugin | Tú mantienes el servidor (parches, actualizaciones, seguridad del propio Jenkins); curva de aprendizaje más alta |

**Decisión:** GitHub Actions
**Justificación:** Es gratis para mi situación y me sirve para aprender las bases.

### Container

| Opción | Pros | Contras |
|--------|------|---------|
| Docker | Estándar de facto, toda la documentación y tutoriales lo asumen, se integra con todo (Trivy, GitHub Actions, etc.) | Docker Desktop tiene licencia paga para empresas grandes |
| Podman | Sin daemon corriendo como root (más seguro por diseño), gratis siempre | Menos compatible con algunas herramientas que asumen Docker; comunidad menor |

**Decisión:** Docker
**Justificación:** Perfecto para aprender y para que el pipeline con Trivy y Sonar funcionen bien.

### Herramientas de Seguridad

| Herramienta | Para qué | Alternativa descartada |
|-------------|----------|----------------------|
| Gitleaks | Secret scanning (detectar keys/passwords en el código) | TruffleHog — más pesado, más falsos positivos |
| SonarCloud | SAST (análisis estático de código) | Checkmarx/Veracode — de pago, fuera de presupuesto |
| Trivy | SCA + escaneo de imagen de contenedor | Snyk — free tier más limitado que Trivy (que es 100% gratis y open source) |
| OWASP ZAP | DAST (probar la app corriendo, buscando vulns como XSS/SQLi en runtime) | Burp Suite — la versión completa es de pago; la Community es muy limitada |

### Monitoreo (Salud del sistema)

| Opción | Pros | Contras |
|--------|------|---------|
| Prometheus + Grafana | Open source, estándar de la industria, tú controlas todo, gratis | Tú lo hosteas y mantienes; curva de aprendizaje para PromQL |
| Datadog / New Relic | Todo integrado, dashboards listos, alertas fáciles | De pago (con free tier muy limitado) |
| ELK Stack | Excelente para logs centralizados, muy usado en la industria | Consume bastantes recursos; más complejo de levantar que Prometheus/Grafana |

**Decisión:** Prometheus + Grafana
**Justificación:** Es gratis y liviano para aprender.

## Decisiones de Arquitectura

### Patrón de diseño
- **Elegido:** Arquitectura en capas
- **Por qué:** Separa dominio, aplicación, infraestructura y presentación. Evita microservicios a menos que el proyecto realmente lo justifique.

### Autenticación
- **Mecanismo:** JWT (JSON Web Tokens)
- **Por qué:** JWT es el estándar para APIs REST — stateless, funciona bien con .NET Identity. Alternativa descartada: sesiones tradicionales (no escalan igual de bien para una API que consumirá un frontend separado).

### Validación de inputs
- **Herramienta:** FluentValidation
- **Por qué:** Separa las reglas de validación del modelo, fácil de testear. Alternativa: Data Annotations nativas de .NET (más simples pero menos flexibles).

## Paquetes NuGet instalados

### GestionDeportiva.API

| Paquete                                               | Para qué                                              | Alternativa descartada                  | Por qué se descartó                                                                              |
| ----------------------------------------------------- | ----------------------------------------------------- | --------------------------------------- | ------------------------------------------------------------------------------------------------ |
| `Microsoft.AspNetCore.Authentication.JwtBearer`       | Autenticación JWT — valida tokens en cada request     | Cookie Authentication, API Keys         | Cookies no funcionan con APIs stateless; API Keys no tienen claims ni roles                      |
| `FluentValidation.AspNetCore`                         | Validación de inputs con reglas declarativas          | Data Annotations nativas de .NET        | Data Annotations son menos flexibles, difíciles de testear, y acopladas al modelo                |
| `Swashbuckle.AspNetCore`                              | Documentación automática de la API (Swagger UI)       | NSwag                                   | Swagger es el estándar de facto; NSwag es más potente pero más complejo de configurar            |
| `AutoMapper.Extensions.Microsoft.DependencyInjection` | Mapear entidades ↔ DTOs sin código manual             | Manually mapping (hand-written)         | Mappers manuales son propensos a errores y tediosos de mantener con muchas entidades             |
| `AspNetCore.HealthChecks.SqlServer`                   | Endpoint `/health` que verifica conexión a SQL Server | —                                       | Es el paquete estándar; se integra con Kubernetes/Docker health probes                           |
| `AspNetCore.HealthChecks.UI`                          | Dashboard visual de health checks                     | —                                       | Útil en desarrollo para ver el estado de las dependencias                                        |
| `Serilog.AspNetCore`                                  | Logging estructurado (JSON) con formato consistente   | `Microsoft.Extensions.Logging` (nativo) | El logging nativo es texto plano; Serilog genera JSON buscable y correlacionable entre servicios |
| `Serilog.Sinks.Console`                               | Envía logs a la consola (desarrollo)                  | —                                       | Sink básico para desarrollo                                                                      |
| `Serilog.Sinks.File`                                  | Envía logs a archivos (producción)                    | —                                       | Persistencia de logs en disco                                                                    |

### GestionDeportiva.DataAccess

| Paquete                                   | Para qué                                                      | Alternativa descartada      | Por qué se descartó                                                                                         |
| ----------------------------------------- | ------------------------------------------------------------- | --------------------------- | ----------------------------------------------------------------------------------------------------------- |
| `Microsoft.EntityFrameworkCore`           | ORM — mapea entidades C# a tablas SQL                         | Dapper, ADO.NET puro        | Dapper es más rápido pero cero automatización; ADO.NET es muy verboso para proyectos con muchas entidades   |
| `Microsoft.EntityFrameworkCore.SqlServer` | Driver de conexión a SQL Server                               | Npgsql (PostgreSQL), Sqlite | SQL Server elegido por escalabilidad servidor-cliente; SQLite es para prototipos, no para concurrencia real |
| `Microsoft.EntityFrameworkCore.Tools`     | Migraciones (`dotnet ef migrations add`)                      | —                           | Necesario para generar migraciones desde la línea de comandos                                               |
| `Microsoft.EntityFrameworkCore.Design`    | Scaffolding de migraciones (genera código C# desde el modelo) | —                           | Sin esto, `dotnet ef` no puede crear archivos de migración                                                  |

### GestionDeportiva.Domain

| Paquete | Para qué | Alternativa descartada | Por qué se descartó |
|---------|----------|----------------------|-------------------|
| `Microsoft.Extensions.Logging.Abstractions` | Interfaces de logging (`ILogger<T>`) sin depender de infraestructura | Referenciar directamente `Serilog` | El Domain debe ser puro — si referenciás Serilog, acoplás el dominio a una implementación específica de logging |

## Decisiones pendientes
<!-- Actualiza a medida que tomas decisiones -->
- [ ] Branching strategy (Git Flow, Trunk-based, etc.)
- [ ] Refresh tokens: sí/no, duración
- [ ] Estrategia de paginación
- [ ] Estrategia de logging (Serilog con qué sinks)

## Referencia
- [[DevSecOps-Proyecto/00-Visión-General]]
- [[DevSecOps-Proyecto/01-Arquitectura]]
- [[DevSecOps-Proyecto/02-Threat-Model]]

---
#proyecto #devsecops #decisiones-tecnicas #gestion-deportiva
