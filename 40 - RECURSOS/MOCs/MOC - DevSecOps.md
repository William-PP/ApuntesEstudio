---
type: moc
state: activa
created: 2026-08-15
updated: 2026-08-15
tags: [config, devsecops, containers, owasp, database, estudio, git, logging, vulnerabilidades, secrets, testing, index, navegacion, deploy, monitoring, checklist]
aliases: [Devsecops]
---

# MOC - DevSecOps
---

## RUTA DE ESTUDIO — Guía de Lectura

### Nivel 1: Fundamentos (Empezar aquí)

📚 Índices del tema: [[40 - RECURSOS/Conceptos/Introduccion-AppSec-DevSecOps]] · [[40 - RECURSOS/MOCs/MOC - Seguridad]] · [[40 - RECURSOS/MOCs/MOC - Patrones]]

| #   | Nota                         | QUÉ aprenderás                            | Tiempo |
| --- | ---------------------------- | ----------------------------------------- | ------ |
| 1   | Introduccion-AppSec-DevSecOps | Qué es AppSec, Shift Left, flujo completo | 15 min |
| 2   | Bases-Datos-Vulnerabilidades  | CVE, NVD, CVSS, OWASP Top 10:2025, CWE    | 20 min |
| 3   | Best-Practices                | Checklist de cosas que SIEMPRE hacer      | 10 min |
| 4   | Anti-Patrones-Seguridad       | Qué NUNCA hacer (10 errores comunes)      | 15 min |

> **Prerequisito para:** Todo lo demás.

---

### Nivel 2: Seguridad en Código

📚 Índice del tema: [[40 - RECURSOS/MOCs/MOC - Seguridad]]

| #   | Nota                         | QUÉ aprenderás                        | Tiempo |
| --- | ---------------------------- | ------------------------------------- | ------ |
| 5   | Injection                    | SQL Injection, XSS, Command Injection | 20 min |
| 6   | Autenticación-y-Autorización | JWT, OAuth, Session, roles            | 20 min |
| 7   | CORS                         | Cross-Origin, config en .NET          | 10 min |
| 8   | Rate-Limiting                | Estrategias, config por endpoint      | 10 min |
| 9   | Encryption                   | Hash, TLS, AES, bcrypt                | 15 min |

> **Prerequisito para:** Testing y CI/CD.

---

### Nivel 3: Testing Automatizado

📚 Índice del tema: [[40 - RECURSOS/MOCs/MOC - .NET]]

| #   | Nota         | QUÉ aprenderás                    | Tiempo |
| --- | ------------ | --------------------------------- | ------ |
| 10  | User-Secrets | Gestión de secretos en desarrollo | 10 min |
| 11  | Health-Checks | Liveness, Readiness, config .NET  | 10 min |

> **Prerequisito para:** Docker.

---

### Nivel 4: Docker y Contenedores

📚 Índices del tema: [[40 - RECURSOS/MOCs/MOC - Docker]] · [[40 - RECURSOS/MOCs/MOC - Cheatsheets]]

| # | Nota | QUÉ aprenderás | Tiempo |
|---|------|---------------|--------|
| 12 | Fundamentos | Conceptos, comandos, multi-stage | 20 min |
| 13 | Dockerfile-Seguro | Non-root, .dockerignore, template | 15 min |
| 14 | Docker-Compose | Orquestar app + BD localmente | 15 min |
| 15 | Trivy | Escaneo de vulnerabilidades en imágenes | 10 min |
| 16 | Comandos-Docker | Referencia rápida de comandos | 5 min |

> **Prerequisito para:** CI/CD.

---

### Nivel 5: CI/CD con GitHub Actions

📚 Índice del tema: [[40 - RECURSOS/MOCs/MOC - CI-CD]]

| # | Nota | QUÉ aprenderás | Tiempo |
|---|------|---------------|--------|
| 17 | GitHub-Actions | Workflow, triggers, jobs, steps | 20 min |
| 18 | SAST | Análisis estático de código | 15 min |
| 19 | SCA | Escaneo de dependencias NuGet | 15 min |
| 20 | Secret-Scanning | Gitleaks, pre-commit hooks | 10 min |

> **Prerequisito para:** IaC.

---

### Nivel 6: Infraestructura como Código

📚 Índice del tema: [[40 - RECURSOS/MOCs/MOC - IaC]]

| # | Nota | QUÉ aprenderás | Tiempo |
|---|------|---------------|--------|
| 21 | Terraform | Providers, resources, state, tfsec | 20 min |

> **Prerequisito para:** Kubernetes.

---

### Nivel 7: Kubernetes

📚 Índices del tema: [[40 - RECURSOS/MOCs/MOC - Kubernetes]] · [[40 - RECURSOS/MOCs/MOC - Cheatsheets]]

| # | Nota | QUÉ aprenderás | Tiempo |
|---|------|---------------|--------|
| 22 | Fundamentos | Pods, Deployments, Services, Ingress | 20 min |
| 23 | Security-Context | runAsNonRoot, capabilities, seccomp | 15 min |
| 24 | Network-Policies | Micro-segmentación, zero trust | 15 min |
| 25 | Comandos-Kubernetes | Referencia rápida de comandos | 5 min |

> **Prerequisito para:** Monitoreo.

---

### Nivel 8: Monitoreo y Observabilidad

📚 Índice del tema: [[40 - RECURSOS/MOCs/MOC - Monitoreo]]

| # | Nota | QUÉ aprenderás | Tiempo |
|---|------|---------------|--------|
| 26 | Structured-Logging | Serilog, JSON logs, niveles | 15 min |
| 27 | Prometheus | Métricas HTTP, alertas | 15 min |
| 28 | Grafana | Dashboards, paneles de seguridad | 15 min |

---

### Nivel 9: Referencia y Troubleshooting

📚 Índices del tema: [[40 - RECURSOS/MOCs/MOC - Seguridad]] · [[40 - RECURSOS/MOCs/MOC - Cheatsheets]] · [[40 - RECURSOS/MOCs/MOC - Troubleshooting]]

| # | Nota | QUÉ aprenderás | Tiempo |
|---|------|---------------|--------|
| 29 | SAST-Detallado | Taint analysis, Semgrep, SonarQube | 15 min |
| 30 | DAST-Detallado | OWASP ZAP, Burp Suite | 15 min |
| 31 | Comandos-Git | Referencia rápida de comandos | 5 min |
| 32 | Errores-Comunes | .NET, Docker, K8s, CI/CD | 20 min |

---

## Mapa de conexiones

```
NIVEL 1: FUNDAMENTOS
┌─────────────────────────────────────────┐
│ 01-Introduccion AppSec                  │
│    │                                    │
│    ├──► Bases-Datos-Vulnerabilidades    │
│    │       (CVE, OWASP, CVSS)           │
│    │                                    │
│    ├──► Best-Practices                  │
│    │                                    │
│    └──► Anti-Patrones                   │
└─────────────────────────────────────────┘
                   │
                   ▼
NIVEL 2: SEGURIDAD EN CÓDIGO
┌─────────────────────────────────────────┐
│ Injection ──► Autenticación ──► CORS    │
│    │              │             │       │
│    │              ▼             ▼       │
│    │         Rate-Limiting  Encryption  │
│    └─────────────────────────────────── │
└─────────────────────────────────────────┘
                   │
                   ▼
NIVEL 3-4: .NET + DOCKER
┌─────────────────────────────────────────┐
│ User-Secrets ──► Health-Checks          │
│       │              │                  │
│       ▼              ▼                  │
│ Docker Fundamentos ──► Dockerfile       │
│       │              Seguro             │
│       ▼              │                  │
│ Docker-Compose    Trivy ──► Cheatsheet  │
└─────────────────────────────────────────┘
                   │
                   ▼
NIVEL 5: CI/CD
┌─────────────────────────────────────────┐
│ GitHub-Actions                          │
│    │                                    │
│    ├──► SAST (análisis estático)        │
│    ├──► SCA (dependencias)              │
│    └──► Secret-Scanning (Gitleaks)      │
└─────────────────────────────────────────┘
                   │
                   ▼
NIVEL 6-7: IaC + KUBERNETES
┌─────────────────────────────────────────┐
│ Terraform ──► Kubernetes Fundamentos    │
│                   │                     │
│                   ├──► Security-Context │
│                   └──► Network-Policies │
└─────────────────────────────────────────┘
                   │
                   ▼
NIVEL 8: MONITOREO
┌─────────────────────────────────────────┐
│ Structured-Logging                      │
│       │                                 │
│       ▼                                 │
│ Prometheus ──► Grafana                  │
└─────────────────────────────────────────┘
```

---

## Guía por objetivo

> [!tip] Cómo usar
> Cada ruta indica qué índices (MOCs) abrir y en qué orden. Dentro de cada uno está el detalle de sus notas.

### "Quiero entender DevSecOps rápido"
[[40 - RECURSOS/Conceptos/Introduccion-AppSec-DevSecOps]] → [[40 - RECURSOS/MOCs/MOC - Seguridad]] → [[40 - RECURSOS/MOCs/MOC - Docker]] → [[40 - RECURSOS/MOCs/MOC - CI-CD]] → [[40 - RECURSOS/MOCs/MOC - Monitoreo]]

### "Quiero escribir código seguro"
[[40 - RECURSOS/Conceptos/Introduccion-AppSec-DevSecOps]] → [[40 - RECURSOS/MOCs/MOC - Seguridad]] → [[40 - RECURSOS/MOCs/MOC - Patrones]]

### "Quiero configurar CI/CD con seguridad"
[[40 - RECURSOS/MOCs/MOC - Docker]] → [[40 - RECURSOS/MOCs/MOC - CI-CD]] → [[40 - RECURSOS/MOCs/MOC - Cheatsheets]]

### "Quiero aprender Docker seguro"
[[40 - RECURSOS/MOCs/MOC - Docker]] → [[40 - RECURSOS/MOCs/MOC - Cheatsheets]]

### "Quiero aprender Kubernetes seguro"
[[40 - RECURSOS/MOCs/MOC - Kubernetes]] → [[40 - RECURSOS/MOCs/MOC - Cheatsheets]]

### "Quiero referencia rápida"
Ir directamente a: [[40 - RECURSOS/MOCs/MOC - Cheatsheets]] | [[40 - RECURSOS/MOCs/MOC - Troubleshooting]]

---

## Contenido por carpeta (MOCs)

Cada carpeta tiene su índice con el detalle de cada nota:

- [[40 - RECURSOS/MOCs/MOC - .NET]] — Secretos y health checks en .NET
- [[40 - RECURSOS/MOCs/MOC - Cheatsheets]] — Referencias rápidas de comandos
- [[40 - RECURSOS/MOCs/MOC - CI-CD]] — Pipeline: GitHub Actions, SAST, SCA, secret scanning
- [[40 - RECURSOS/MOCs/MOC - Docker]] — Contenedores seguros y escaneo
- [[40 - RECURSOS/MOCs/MOC - IaC]] — Infraestructura como código
- [[40 - RECURSOS/MOCs/MOC - Kubernetes]] — Seguridad de clúster
- [[40 - RECURSOS/MOCs/MOC - Monitoreo]] — Logs, métricas y dashboards
- [[40 - RECURSOS/MOCs/MOC - Patrones]] — Best practices y anti-patrones
- [[40 - RECURSOS/MOCs/MOC - Seguridad]] — AppSec y herramientas
- [[40 - RECURSOS/MOCs/MOC - Troubleshooting]] — Errores comunes

---

## Notas de estudio
<!-- Agrega aquí cosas que vayas aprendiendo y no encajen en otra nota -->
- 

## Links Relacionados
- [[Flujo de un proyecto]]
- [[40 - RECURSOS/MOCs/MOC - Ciberseguridad]]

## Referencia
- [[MOC - DevSecOps]]

#config #devsecops #containers #owasp #database #estudio #git #logging #vulnerabilidades #secrets #testing #index #navegacion #deploy #monitoring #checklist
