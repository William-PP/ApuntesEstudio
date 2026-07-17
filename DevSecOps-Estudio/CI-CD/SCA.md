# SCA (Software Composition Analysis)

## QUÉ es

Escaneo de **dependencias/librerías** que usa tu proyecto para detectar vulnerabilidades conocidas (CVEs).

## POR QUÉ importa

| Sin SCA | Con SCA |
|---------|---------|
| Usas librerías con CVEs sin saberlo | Alertas automáticas de vulnerabilidades |
| Dependencias desactualizadas | Actualizaciones automáticas (Dependabot) |
| Sin visibility de supply chain | Reporte completo de dependencias |

## Ejemplo real

```
Tu proyecto usa Newtonsoft.Json 12.0.0
                    │
                    ▼
SCA detecta: Newtonsoft.Json < 13.0.1 tiene CVE-2024-XXXXX
                    │
                    ▼
Acción: Actualizar a versión 13.0.1 o superior
```

## CÓMO ejecutar en .NET

```bash
# Ver paquetes vulnerables
dotnet list package --vulnerable

# Incluyendo dependencias transitivas
dotnet list package --vulnerable --include-transitive

# Output
The following packages have known vulnerabilities:
Json.NET 12.0.0 [Critical: CVE-2024-XXXXX]
```

## Herramientas

| Herramienta | Cuándo | Costo |
|------------|--------|-------|
| `dotnet list package --vulnerable` | Local y CI | Gratis |
| Dependabot | Automático en GitHub | Gratis |
| Snyk | CI/CD | Free tier |
| OWASP Dependency-Check | CI/CD | Gratis |

## Dependabot

### `.github/dependabot.yml`
```yaml
version: 2
updates:
  - package-ecosystem: "nuget"
    directory: "/"
    schedule:
      interval: "weekly"
    open-pull-requests-limit: 10
```

**Qué hace:** Cada semana busca actualizaciones y crea PRs automáticos.

## CUÁNDO ejecutar

| Momento | Herramienta |
|---------|------------|
| Local | `dotnet list package --vulnerable` |
| Cada push | Dependabot |
| CI pipeline | Step en workflow |
| Semanal | Dependabot schedule |

## Referencia
- [[DevSecOps-Estudio/00-Index]]

#ci-cd #devsecops #owasp #estudio #git #vulnerabilidades #sca #dependencias #github-actions
