# Bases de Datos de Vulnerabilidades

## QUÉ son

Repositorios públicos que registran vulnerabilidades de seguridad conocidas. Las herramientas de seguridad las usan para identificar riesgos.

## Principales bases

### CVE (Common Vulnerabilities and Exposures)

| Aspecto | Detalle |
|---------|---------|
| **QUÉ** | Identificador estándar de vulnerabilidades |
| **Formato** | CVE-YYYY-NNNNN (ej: CVE-2024-21762) |
| **DÓNDE** | cve.mitre.org |
| **CUÁNDO** | Cuando se descubre una nueva vulnerabilidad |

```
Ejemplo:
CVE-2024-21762
│         │     │
│         │     └─ Número secuencial
│         └─ Año de publicación
└─ Formato estándar
```

### NVD (National Vulnerability Database)

| Aspecto     | Detalle                                              |
| ----------- | ---------------------------------------------------- |
| **QUÉ**     | Base oficial mantenida por NIST (gobierno de EE.UU.) |
| **DÓNDE**   | nvd.nist.gov                                         |
| **CUÁNDO**  | Se actualiza constantemente                          |
| **Incluye** | CVE + puntuación CVSS + recomendaciones              |

### CVSS (Common Vulnerability Scoring System)

Puntuación de severidad de 0 a 10:

| Score      | Severidad | Color |
| ---------- | --------- | ----- |
| 0.0 - 3.9  | Low       | 🟢    |
| 4.0 - 6.9  | Medium    | 🟡    |
| 7.0 - 8.9  | High      | 🟠    |
| 9.0 - 10.0 | Critical  | 🔴    |

> **Nota:** Existen CVSS v2, v3.x y v4.0. La mayoría de bases hoy reportan v3.x; v4.0 aún tiene adopción parcial. Si ves dos scores distintos para el mismo CVE, revisa qué versión de CVSS se usó.

### OWASP (Open Web Application Security Project)

| Aspecto          | Detalle                                    |
| ---------------- | ------------------------------------------ |
| **QUÉ**          | Guías de seguridad y rankings              |
| **Más conocido** | OWASP Top 10                               |
| **DÓNDE**        | owasp.org                                  |
| **CUÁNDO**       | Se actualiza aproximadamente cada 3-4 años |

> **Actualización importante:** La versión vigente es el **OWASP Top 10:2025**, presentada en noviembre de 2025 en la OWASP Global AppSec Conference (Washington D.C.), con versión final publicada en enero de 2026. Reemplaza la edición 2021. Dos categorías nuevas y SSRF se fusionó con Broken Access Control.

### OWASP Top 10:2025 (versión vigente)

| #   | Vulnerabilidad                         | Cambio vs. 2021                                                                                    |
| --- | -------------------------------------- | -------------------------------------------------------------------------------------------------- |
| A01 | Broken Access Control                  | Se mantiene #1 — ahora incluye SSRF explícitamente                                                 |
| A02 | Security Misconfiguration              | Sube de #5 a #2                                                                                    |
| A03 | Software Supply Chain Failures         | **Nueva** — expansión de "Vulnerable Components", cubre todo el ecosistema de dependencias y CI/CD |
| A04 | Cryptographic Failures                 | Baja de #2 a #4                                                                                    |
| A05 | Injection                              | Baja de #3 a #5 (SQLi en declive, XSS/SSTI siguen relevantes)                                      |
| A06 | Insecure Design                        | Baja de #4 a #6                                                                                    |
| A07 | Authentication Failures                | Se mantiene #7 (antes "Identification and Authentication Failures")                                |
| A08 | Software or Data Integrity Failures    | Se mantiene #8                                                                                     |
| A09 | Security Logging and Alerting Failures | Se mantiene #9 (ahora enfatiza también la alerta, no solo el log)                                  |
| A10 | Mishandling of Exceptional Conditions  | **Nueva** — manejo pobre de errores/excepciones (input validation incompleta, "failing open")      |

### OWASP Top 10 (2021 — histórica, útil como referencia)

| #   | Vulnerabilidad            | Descripción                                            |
| --- | ------------------------- | ------------------------------------------------------ |
| A01 | Broken Access Control     | Acceso no autorizado a recursos                        |
| A02 | Cryptographic Failures    | Encriptación débil o ausente                           |
| A03 | Injection                 | SQL, XSS, Command injection                            |
| A04 | Insecure Design           | Diseño sin considerar seguridad                        |
| A05 | Security Misconfiguration | Configs por defecto, debug activo                      |
| A06 | Vulnerable Components     | Dependencias con CVEs                                  |
| A07 | Auth Failures             | Autenticación débil                                    |
| A08 | Data Integrity Failures   | Sin verificación de integridad                         |
| A09 | Logging Failures          | Sin logs de seguridad                                  |
| A10 | SSRF                      | Server-Side Request Forgery (absorbida en A01 en 2025) |

### MITRE

| Aspecto      | Detalle                                                 |
| ------------ | ------------------------------------------------------- |
| **QUÉ**      | Catálogo de debilidades de software                     |
| **Incluye**  | CWE (Common Weakness Enumeration)                       |
| **DÓNDE**    | cwe.mitre.org                                           |
| **Relación** | CWE → describe la debilidad, CVE → instancia específica |

### Relación CWE → CVE

```
CWE-89: SQL Injection (la debilidad general)
    │
    ├── CVE-2024-21762: SQL Injection en FortiOS
    ├── CVE-2024-12345: SQL Injection en WordPress Plugin X
    └── CVE-2024-67890: SQL Injection en App Y

CWE = el TIPO de problema
CVE = cada INSTANCIA específica del problema
```

## Cómo las usan las herramientas

```
Tu app usa Newtonsoft.Json 12.0.0
              │
              ▼
SCA consulta NVD: ¿Newtonsoft.Json 12.0.0 tiene CVEs?
              │
              ▼
NVD responde: Sí, CVE-2024-XXXXX (CVSS 9.8 Critical)
              │
              ▼
Herramienta reporta: ⚠️ Vulnerabilidad encontrada
```

## DÓNDE consultar

| Base            | URL                   | Para qué                   |
| --------------- | --------------------- | -------------------------- |
| NVD             | nvd.nist.gov          | Buscar CVEs específicos    |
| OWASP           | owasp.org/Top10/2025  | Top 10 vigente y guías     |
| CVE             | cve.mitre.org         | Verificar si un CVE existe |
| CWE             | cwe.mitre.org         | Entender debilidades       |
| GitHub Advisory | github.com/advisories | Dependencias de GitHub     |
| NuGet           | nuget.org/packages    | Vulnerabilidades de .NET   |

## Referencia
- [[DevSecOps-Estudio/00-Index]]
- [[Introduccion-AppSec-DevSecOps]]
- [[CI-CD/SCA]]

---
#seguridad #appsec #cve #owasp #cvss #cwe
