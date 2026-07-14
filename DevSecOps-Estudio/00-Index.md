# DevSecOps — Estudio General
---

## RUTA DE ESTUDIO — Guía de Lectura

### Nivel 1: Fundamentos (Empezar aquí)

| #   | Nota                                       | QUÉ aprenderás                            | Tiempo |
| --- | ------------------------------------------ | ----------------------------------------- | ------ |
| 1   | [[Introduccion-AppSec-DevSecOps]]       | Qué es AppSec, Shift Left, flujo completo | 15 min |
| 2   | [[Seguridad/Bases-Datos-Vulnerabilidades]] | CVE, NVD, CVSS, OWASP Top 10:2025, CWE    | 20 min |
| 3   | [[Patrones/Best-Practices]]                | Checklist de cosas que SIEMPRE hacer      | 10 min |
| 4   | [[Patrones/Anti-Patrones-Seguridad]]       | Qué NUNCA hacer (10 errores comunes)      | 15 min |

> **Prerequisito para:** Todo lo demás.

---

### Nivel 2: Seguridad en Código

| #   | Nota                                       | QUÉ aprenderás                        | Tiempo |
| --- | ------------------------------------------ | ------------------------------------- | ------ |
| 5   | [[Seguridad/Injection]]                    | SQL Injection, XSS, Command Injection | 20 min |
| 6   | [[Seguridad/Autenticación-y-Autorización]] | JWT, OAuth, Session, roles            | 20 min |
| 7   | [[Seguridad/CORS]]                         | Cross-Origin, config en .NET          | 10 min |
| 8   | [[Seguridad/Rate-Limiting]]                | Estrategias, config por endpoint      | 10 min |
| 9   | [[Seguridad/Encryption]]                   | Hash, TLS, AES, bcrypt                | 15 min |

> **Prerequisito para:** Testing y CI/CD.

---

### Nivel 3: Testing Automatizado

| # | Nota | QUÉ aprenderás | Tiempo |
|---|------|---------------|--------|
| 10 | [[.NET/User-Secrets]] | Gestión de secretos en desarrollo | 10 min |
| 11 | [[.NET/Health-Checks]] | Liveness, Readiness, config .NET | 10 min |

> **Prerequisito para:** Docker.

---

### Nivel 4: Docker y Contenedores

| # | Nota | QUÉ aprenderás | Tiempo |
|---|------|---------------|--------|
| 12 | [[Docker/Fundamentos]] | Conceptos, comandos, multi-stage | 20 min |
| 13 | [[Docker/Dockerfile-Seguro]] | Non-root, .dockerignore, template | 15 min |
| 14 | [[Docker/Docker-Compose]] | Orquestar app + BD localmente | 15 min |
| 15 | [[Docker/Trivy]] | Escaneo de vulnerabilidades en imágenes | 10 min |
| 16 | [[Cheatsheets/Comandos-Docker]] | Referencia rápida de comandos | 5 min |

> **Prerequisito para:** CI/CD.

---

### Nivel 5: CI/CD con GitHub Actions

| # | Nota | QUÉ aprenderás | Tiempo |
|---|------|---------------|--------|
| 17 | [[CI-CD/GitHub-Actions]] | Workflow, triggers, jobs, steps | 20 min |
| 18 | [[CI-CD/SAST]] | Análisis estático de código | 15 min |
| 19 | [[CI-CD/SCA]] | Escaneo de dependencias NuGet | 15 min |
| 20 | [[CI-CD/Secret-Scanning]] | Gitleaks, pre-commit hooks | 10 min |

> **Prerequisito para:** IaC.

---

### Nivel 6: Infraestructura como Código

| # | Nota | QUÉ aprenderás | Tiempo |
|---|------|---------------|--------|
| 21 | [[IaC/Terraform]] | Providers, resources, state, tfsec | 20 min |

> **Prerequisito para:** Kubernetes.

---

### Nivel 7: Kubernetes

| # | Nota | QUÉ aprenderás | Tiempo |
|---|------|---------------|--------|
| 22 | [[Kubernetes/Fundamentos]] | Pods, Deployments, Services, Ingress | 20 min |
| 23 | [[Kubernetes/Security-Context]] | runAsNonRoot, capabilities, seccomp | 15 min |
| 24 | [[Kubernetes/Network-Policies]] | Micro-segmentación, zero trust | 15 min |
| 25 | [[Cheatsheets/Comandos-Kubernetes]] | Referencia rápida de comandos | 5 min |

> **Prerequisito para:** Monitoreo.

---

### Nivel 8: Monitoreo y Observabilidad

| # | Nota | QUÉ aprenderás | Tiempo |
|---|------|---------------|--------|
| 26 | [[Monitoreo/Structured-Logging]] | Serilog, JSON logs, niveles | 15 min |
| 27 | [[Monitoreo/Prometheus]] | Métricas HTTP, alertas | 15 min |
| 28 | [[Monitoreo/Grafana]] | Dashboards, paneles de seguridad | 15 min |

---

### Nivel 9: Referencia y Troubleshooting

| # | Nota | QUÉ aprenderás | Tiempo |
|---|------|---------------|--------|
| 29 | [[Seguridad/SAST-Detallado]] | Taint analysis, Semgrep, SonarQube | 15 min |
| 30 | [[Seguridad/DAST-Detallado]] | OWASP ZAP, Burp Suite | 15 min |
| 31 | [[Cheatsheets/Comandos-Git]] | Referencia rápida de comandos | 5 min |
| 32 | [[Troubleshooting/Errores-Comunes]] | .NET, Docker, K8s, CI/CD | 20 min |

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

### "Quiero entender DevSecOps rápido"
Leer en orden: 1 → 2 → 3 → 4 → 5 → 12 → 17 → 26

### "Quiero escribir código seguro"
Leer en orden: 1 → 5 → 6 → 7 → 8 → 9 → 3 → 4

### "Quiero configurar CI/CD con seguridad"
Leer en orden: 12 → 13 → 15 → 17 → 18 → 19 → 20

### "Quiero aprender Docker seguro"
Leer en orden: 12 → 13 → 14 → 15 → 16

### "Quiero aprender Kubernetes seguro"
Leer en orden: 22 → 23 → 24 → 25

### "Quiero referencia rápida"
Ir directamente a: [[Cheatsheets/Comandos-Docker]] | [[Cheatsheets/Comandos-Kubernetes]] | [[Cheatsheets/Comandos-Git]] | [[Troubleshooting/Errores-Comunes]]

---

## Contenido por carpeta

### 📁 Seguridad
| Nota | Descripción | Nivel |
|------|------------|-------|
| [[Seguridad/Bases-Datos-Vulnerabilidades]] | CVE, NVD, CVSS, OWASP Top 10:2025, CWE | 1 |
| [[Seguridad/Injection]] | SQL, XSS, Command injection | 2 |
| [[Seguridad/Autenticación-y-Autorización]] | JWT, OAuth, Session | 2 |
| [[Seguridad/CORS]] | Cross-Origin Resource Sharing | 2 |
| [[Seguridad/Rate-Limiting]] | Limitar peticiones | 2 |
| [[Seguridad/Encryption]] | Hash, TLS, AES | 2 |
| [[Seguridad/SAST-Detallado]] | Análisis estático profundo | 9 |
| [[Seguridad/DAST-Detallado]] | Análisis dinámico profundo | 9 |

### 📁 Docker
| Nota | Descripción | Nivel |
|------|------------|-------|
| [[Docker/Fundamentos]] | Conceptos y comandos | 4 |
| [[Docker/Dockerfile-Seguro]] | Template seguro, multi-stage | 4 |
| [[Docker/Docker-Compose]] | Orquestar servicios | 4 |
| [[Docker/Trivy]] | Escaneo de vulnerabilidades | 4 |

### 📁 CI/CD
| Nota | Descripción | Nivel |
|------|------------|-------|
| [[CI-CD/GitHub-Actions]] | Workflow YAML | 5 |
| [[CI-CD/SAST]] | Análisis estático | 5 |
| [[CI-CD/SCA]] | Dependencias NuGet | 5 |
| [[CI-CD/Secret-Scanning]] | Gitleaks | 5 |

### 📁 Kubernetes
| Nota | Descripción | Nivel |
|------|------------|-------|
| [[Kubernetes/Fundamentos]] | Pods, Deployments, Services | 7 |
| [[Kubernetes/Security-Context]] | Seguridad a nivel Pod | 7 |
| [[Kubernetes/Network-Policies]] | Micro-segmentación | 7 |

### 📁 IaC
| Nota | Descripción | Nivel |
|------|------------|-------|
| [[IaC/Terraform]] | Infraestructura como Código | 6 |

### 📁 Monitoreo
| Nota | Descripción | Nivel |
|------|------------|-------|
| [[Monitoreo/Structured-Logging]] | Serilog, JSON logs | 8 |
| [[Monitoreo/Prometheus]] | Métricas HTTP | 8 |
| [[Monitoreo/Grafana]] | Dashboards | 8 |

### 📁 .NET
| Nota | Descripción | Nivel |
|------|------------|-------|
| [[.NET/User-Secrets]] | Gestión de secretos | 3 |
| [[.NET/Health-Checks]] | Liveness, Readiness | 3 |

### 📁 Cheatsheets
| Nota | Descripción | Nivel |
|------|------------|-------|
| [[Cheatsheets/Comandos-Docker]] | Referencia Docker | 4 |
| [[Cheatsheets/Comandos-Kubernetes]] | Referencia K8s | 7 |
| [[Cheatsheets/Comandos-Git]] | Referencia Git | 5 |

### 📁 Patrones
| Nota | Descripción | Nivel |
|------|------------|-------|
| [[Patrones/Best-Practices]] | Qué SIEMPRE hacer | 1 |
| [[Patrones/Anti-Patrones-Seguridad]] | Qué NUNCA hacer | 1 |

### 📁 Troubleshooting
| Nota | Descripción | Nivel |
|------|------------|-------|
| [[Troubleshooting/Errores-Comunes]] | Errores .NET, Docker, K8s, CI/CD | 9 |

---

## Notas de estudio
<!-- Agrega aquí cosas que vayas aprendiendo y no encajen en otra nota -->
- 

## Links Relacionados
- [[DevSecOps-Proyecto/00-Visión-General]]
- [[Ciberseguridad/00-Index]]
