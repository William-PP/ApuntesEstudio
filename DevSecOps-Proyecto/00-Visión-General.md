# Visión General del Proyecto

## ¿Qué es este proyecto?
<!-- Describe brevemente de qué trata tu aplicación -->
[Tu proyecto aquí]

## Objetivo
Crear una aplicación funcional aplicando **DevSecOps** de principio a fin, documentando cada fase del proceso.

## Stack Tecnológico
| Capa | Tecnología | Justificación |
|------|-----------|---------------|
| Lenguaje | | |
| Framework | | |
| Base de datos | | |
| Container | | |
| CI/CD | | |
| Cloud | | |
| Monitoreo | | |

## Fases del Proyecto

### Fase -1: Diseño Seguro y Setup
- [ ] Definir tema del proyecto
- [ ] Threat Modeling (STRIDE)
- [ ] Diseñar arquitectura por capas
- [ ] Configurar repo (.gitignore, secrets, git hooks)
- [ ] Definir branching strategy
- [ ] Documentar decisiones técnicas

### Fase 0: Seguridad en Código (Shift Left)
- [ ] Autenticación JWT
- [ ] CORS policy
- [ ] Rate Limiting
- [ ] Input Validation (FluentValidation)
- [ ] Health Checks
- [ ] Secrets Management (User Secrets / env vars)

### Fase 1: Testing Automatizado
- [ ] Unit Tests (xUnit + Moq)
- [ ] Integration Tests (WebApplicationFactory)
- [ ] Testcontainers para SQL Server
- [ ] Code Coverage (>70%)

### Fase 2: Docker Seguro
- [ ] Dockerfile multi-stage
- [ ] .dockerignore
- [ ] Non-root user
- [ ] docker-compose.yml
- [ ] Escaneo con Trivy

### Fase 3: CI/CD con GitHub Actions
- [ ] Workflow Build + Test
- [ ] SAST (análisis estático)
- [ ] SCA (escaneo de dependencias)
- [ ] Secret Scan (Gitleaks)
- [ ] Docker Build + Trivy
- [ ] Dependabot
- [ ] Quality Gates

### Fase 4: Infraestructura como Código
- [ ] Terraform setup
- [ ] Provisionar recursos cloud
- [ ] IaC Scanning (tfsec/checkov)
- [ ] Remote State

### Fase 5: Kubernetes Seguro
- [ ] Deployment manifests
- [ ] Security Context
- [ ] Network Policies
- [ ] RBAC
- [ ] Sealed Secrets

### Fase 6: Monitoreo y Observabilidad
- [ ] Serilog (structured logging)
- [ ] Prometheus (metrics)
- [ ] Grafana (dashboards)
- [ ] Alertas de seguridad
- [ ] Security logging

## Estado Actual
**Fase en progreso:** Fase -1
**Última actualización:** 2026-07-14

## Links Relacionados
- [[01-Arquitectura]]
- [[02-Threat-Model]]
- [[03-Decisiones-Técnicas]]
- [[Glosario-DevSecOps]]
