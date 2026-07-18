---
tags: [proyecto/gestion-deportiva, devsecops, fase-1]
created: 2026-07-14
updated: 2026-07-18
---

# Visión General del Proyecto

## ¿Qué es este proyecto?
Mi proyecto es de gestión deportiva de judo. La idea es facilitar a los entrenadores y monitores del deporte el seguimiento de los practicantes de judo: desde la gestión de torneos, categorías, modalidades, inscripciones y resultados.

## Objetivo
Crear una aplicación funcional aplicando **DevSecOps** de principio a fin, documentando cada fase del proceso.

## Stack Tecnológico

| Capa | Tecnología | Justificación |
|------|-----------|---------------|
| Lenguaje | C# | Conocimiento previo del lenguaje; en el futuro pretendo indagar más en otros |
| Framework | .NET 10 | Ver [[DevSecOps-Proyecto/03-Decisiones-Técnicas]] |
| Base de datos | SQL Server | Ver [[DevSecOps-Proyecto/03-Decisiones-Técnicas]] |
| ORM | Entity Framework Core | Ver [[DevSecOps-Proyecto/03-Decisiones-Técnicas]] |
| Container | Docker | Ver [[DevSecOps-Proyecto/03-Decisiones-Técnicas]] |
| CI/CD | GitHub Actions | Ver [[DevSecOps-Proyecto/03-Decisiones-Técnicas]] |
| Seguridad | Gitleaks, SonarCloud, Trivy, OWASP ZAP | Ver [[DevSecOps-Proyecto/03-Decisiones-Técnicas]] |
| Monitoreo | Prometheus + Grafana | Ver [[DevSecOps-Proyecto/03-Decisiones-Técnicas]] |
| Cloud | Pendiente (último paso) | |

## Fases del Proyecto

### Fase -1: Diseño Seguro y Setup
- [x] Definir tema del proyecto
- [ ] Threat Modeling (STRIDE)
- [x] Diseñar arquitectura por capas
- [ ] Configurar repo (.gitignore, secrets, git hooks)
- [ ] Definir branching strategy
- [x] Documentar decisiones técnicas

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
**Última actualización:** 2026-07-18

## Referencia
- [[DevSecOps-Proyecto/01-Arquitectura]]
- [[DevSecOps-Proyecto/02-Threat-Model]]
- [[DevSecOps-Proyecto/03-Decisiones-Técnicas]]
- [[DevSecOps-Proyecto/GestionDeportiva-Diccionario-Datos]]
- [[DevSecOps-Proyecto/GestionDeportiva-Diagrama-Clases]]
- [[DevSecOps-Proyecto/Referencia/Glosario-DevSecOps]]

---
#config #devsecops #proyecto #gestion-deportiva
