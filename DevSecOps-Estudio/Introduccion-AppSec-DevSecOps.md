# Introducción a Seguridad de Aplicaciones y Automatización DevSecOps

## 1. Contexto y utilidad

La seguridad de aplicaciones (**Application Security** o **AppSec**) es la disciplina que busca **prevenir, detectar y corregir vulnerabilidades** dentro del software durante todo su ciclo de vida de desarrollo (SDLC — Software Development Life Cycle).

### El problema del enfoque tradicional

Tradicionalmente, las pruebas de seguridad se realizaban al final del desarrollo o durante auditorías. Este enfoque generaba:

| Problema                                    | Consecuencia                               |
| ------------------------------------------- | ------------------------------------------ |
| Vulnerabilidades detectadas demasiado tarde | Arreglos complejos y costosos              |
| Costo de corrección alto                    | Presupuesto y tiempo extra                 |
| Errores llegaban a producción               | Brechas de seguridad, pérdida de confianza |

### La solución: Shift-Left Security

**Shift-Left Security** = mover las pruebas de seguridad hacia las **primeras etapas del desarrollo**, integrándolas directamente en el flujo de trabajo del desarrollador y en los pipelines de CI/CD.

```
TRADICIONAL:
Code → Build → Test → Deploy → 🔍 SEGURIDAD (¡tarde!)

SHIFT LEFT:
🔍 Design → 🔍 Code → 🔍 Build → 🔍 Test → 🔍 Deploy → 🔍 Monitor
```

### Cuándo se ejecutan las verificaciones

En este modelo, las verificaciones de seguridad se ejecutan automáticamente cuando:

| Momento                      | Herramienta típica        |
| ---------------------------- | ------------------------- |
| Se **escribe código**        | SAST (análisis estático)  |
| Se realiza un **commit**     | Secret scanning           |
| Se **compila** el proyecto   | SAST + SCA                |
| Se **ejecuta** la aplicación | DAST (análisis dinámico)  |
| Se **deploya**               | Container scan + IaC scan |

> **Regla mental:** Mientras más temprano en el SDLC detectas un bug de seguridad, más barato es arreglarlo. Un fallo encontrado en código cuesta centavos; el mismo fallo en producción puede costar miles de dólares y reputación.

---

## 2. Principales ramas de la seguridad en aplicaciones

La seguridad moderna del software se divide en varias áreas especializadas. Cada una detecta un tipo distinto de vulnerabilidad.

### SAST — Static Application Security Testing

**Qué es:** Analiza el **código fuente sin ejecutar** la aplicación.

**Qué examina:**
- Estructuras de código
- Flujos de datos
- Uso de funciones inseguras

**Qué detecta:**
- SQL Injection
- Cross-Site Scripting (XSS)
- Path Traversal
- Credenciales embebidas (hardcoded secrets)

**Herramientas:** [[CI-CD/SAST|Semgrep]], SonarQube, SonarLint

**Ver más:** [[Seguridad/SAST-Detallado]]

---

### SCA — Software Composition Analysis

**Qué es:** Analiza las **dependencias externas** del software.

**El problema:** La mayoría de aplicaciones modernas usan numerosas librerías externas. Si alguna contiene vulnerabilidades conocidas, la aplicación también queda expuesta.

**Qué detecta:**
- Librerías vulnerables (CVEs)
- Dependencias transitivas inseguras
- Problemas de licenciamiento

**Herramientas:** OWASP Dependency-Check, Snyk, Trivy

**Ver más:** [[CI-CD/SCA|SCA Detallado]]

---

### DAST — Dynamic Application Security Testing

**Qué es:** Analiza la aplicación **mientras está ejecutándose**.

**Cómo funciona:** El escáner interactúa con la aplicación como lo haría un atacante, enviando solicitudes manipuladas para detectar vulnerabilidades.

**Qué detecta:**
- SQL Injection
- Cross-Site Scripting
- Errores de autenticación
- Configuraciones inseguras

**Herramientas:** OWASP ZAP, Burp Suite

**Ver más:** [[Seguridad/DAST-Detallado]]

---

### Seguridad de Contenedores

Las aplicaciones modernas se ejecutan dentro de contenedores que incluyen:
- Sistema operativo
- Librerías
- Dependencias del software

Si alguna de estas capas tiene vulnerabilidades, la aplicación puede ser comprometida.

**Herramientas:** Trivy, Clair, Docker Scout

**Ver más:** [[Docker/Trivy]]

---

### Seguridad de Infraestructura

Además del código, es necesario revisar la infraestructura donde se ejecuta:
- Servidores
- Puertos abiertos
- Configuraciones del sistema
- Servicios expuestos

**Herramientas:** Nmap, OpenVAS, Nessus

---

## 3. Bases de datos de vulnerabilidades

Las herramientas de seguridad utilizan bases de datos públicas para identificar riesgos conocidos:

| Base                                      | Función                                                                 |
| ----------------------------------------- | ----------------------------------------------------------------------- |
| **CVE**                                   | Identificador estándar de vulnerabilidades                              |
| **NVD** (National Vulnerability Database) | Base oficial mantenida por el gobierno de EE.UU.                        |
| **OWASP**                                 | Guías de seguridad y lista OWASP Top 10                                 |
| **MITRE**                                 | Catálogo de debilidades de software (CWE — Common Weakness Enumeration) |

Estas bases se actualizan constantemente con nuevas vulnerabilidades descubiertas.

**Ver más:** [[Seguridad/Bases-Datos-Vulnerabilidades]]

---

## 4. Automatización con DevSecOps

El enfoque moderno integra todas estas pruebas dentro de pipelines automatizados de CI/CD.

### Flujo típico de seguridad automatizada

```
Developer commit
      │
      ▼
┌─────────────┐
│    SAST      │  ← Análisis de código
└──────┬──────┘
       │
       ▼
┌─────────────┐
│    SCA       │  ← Dependencias
└──────┬──────┘
       │
       ▼
┌─────────────┐
│    BUILD     │
└──────┬──────┘
       │
       ▼
┌─────────────────────────────┐
│  Seguridad de contenedores   │  ← Trivy, Docker Scout
└──────┬──────────────────────┘
       │
       ▼
┌─────────────┐
│    DAST      │  ← Escaneo dinámico
└──────┬──────┘
       │
       ▼
┌─────────────┐
│   DEPLOY     │
└──────┬──────┘
       │
       ▼
┌─────────────────────────────┐
│  Seguridad de infraestructura│  ← Nmap, OpenVAS
└─────────────────────────────┘
```

### Herramientas de CI/CD para automatizar

| Plataforma         | Tipo                      |
| ------------------ | ------------------------- |
| **GitHub Actions** | Integrado en GitHub       |
| **GitLab CI/CD**   | Integrado en GitLab       |
| **Jenkins**        | Self-hosted, muy flexible |

---

## 5. Limitaciones de la automatización

Aunque las herramientas automatizadas son fundamentales, presentan limitaciones:

| Limitación                       | Ejemplo                                                     |
| -------------------------------- | ----------------------------------------------------------- |
| **Falsos positivos**             | Alerta de vulnerabilidad que no es real                     |
| **No detecta lógica de negocio** | Un bypass de regla de negocio no se detecta automáticamente |
| **Requiere mantenimiento**       | Reglas deben actualizarse                                   |

### Complementos necesarios

Por esta razón, las organizaciones complementan con:
- Revisiones de código (code review)
- Auditorías de seguridad
- Pruebas de penetración (pentesting)

---

## 6. Objetivo del estudio

El objetivo de estudiar seguridad de aplicaciones y DevSecOps es desarrollar la capacidad de:

1. **Identificar** vulnerabilidades durante el desarrollo
2. **Automatizar** pruebas de seguridad
3. **Integrar** herramientas de análisis en pipelines
4. **Asegurar** el software antes de su despliegue en producción

Este enfoque permite reducir significativamente el riesgo de incidentes de seguridad en sistemas modernos.

---

## Mis herramientas (referencia rápida)

| Categoría           | Herramientas                            |
| ------------------- | --------------------------------------- |
| **SAST**            | SonarQube (SonarLint para IDE), Semgrep |
| **SCA**             | Snyk, OWASP Dependency-Check            |
| **DAST**            | OWASP ZAP, Burp Suite Community Edition |
| **Contenedores**    | Trivy, Docker Scout                     |
| **Infraestructura** | Nmap, OpenVAS                           |

> **Por qué estas elecciones:**
> - OWASP ZAP y Trivy son gratuitas, open source, y fáciles de integrar en GitHub Actions — buena opción para empezar y para portafolio propio.
> - Semgrep y Snyk tienen planes gratuitos con límites generosos para proyectos personales/académicos.
> - Nmap es el estándar de facto para reconocimiento de red; OpenVAS es gratuito frente a Nessus, que es de pago.

---

## Tags
#seguridad #devsecops #appsec #ciclo-de-vida-software

## Referencia
- [[DevSecOps-Estudio/00-Index]]
- [[02-Threat-Model]]
- [[Patrones/Best-Practices]]
