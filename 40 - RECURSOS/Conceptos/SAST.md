---
type: concepto
state: activa
created: 2026-08-15
updated: 2026-09-05
tags: [config, seguridad, devsecops, database, api, estudio, git, vulnerabilidades, secrets, testing, sast]
---

# SAST — Static Application Security Testing

> [!info] Nota unificada
> Versión consolidada de `SAST` y `SAST-Detallado`: resumen, profundidad, herramientas y configuración en CI/CD.

## QUÉ es

Análisis del **código fuente sin ejecutarlo** para encontrar vulnerabilidades, code smells y malas prácticas de seguridad.

```
Código fuente → Motor de análisis → Reporte de vulnerabilidades
```

## POR QUÉ importa

| Sin SAST | Con SAST |
|----------|----------|
| Bugs de seguridad encontrados en producción | Bugs encontrados al escribir código |
| Code review manual propenso a errores | Análisis automatizado + manual |
| Sin métricas de calidad | Reportes de deuda técnica |

## QUÉ detecta

| Vulnerabilidad | Ejemplo |
|---------------|---------|
| **SQL Injection** | `"SELECT * FROM users WHERE id=" + userId` |
| **XSS** | `return Content(userInput)` sin sanitizar |
| **Path Traversal** | `File.ReadAllText(userPath)` sin validar |
| **Hardcoded Secrets** | `var apiKey = "sk-abc123..."` |
| **Deprecated APIs** | Uso de funciones con known vulnerabilities |
| **Weak Cryptography** | MD5, SHA1 para passwords |

## CÓMO funciona internamente

```
1. Parsea el código fuente en un AST (Abstract Syntax Tree)
         │
         ▼
2. Recorre el árbol buscando patrones inseguros
         │
         ▼
3. Identifica flujos de datos (taint analysis)
         │
         ▼
4. Genera reporte con ubicación exacta del problema
```

### Taint Analysis (Análisis de contaminación)

```
FUENTE (source):    Input del usuario (request body, query params, headers)
SINK (sumidero):    Función peligrosa (SQL query, HTML output, file system)
FLUJO:              Source → Transformaciones → Sink

Si un Source llega a un Sink sin sanitización → VULNERABILIDAD
```

## Herramientas para .NET

| Herramienta | Tipo | Costo | Integración |
|------------|------|-------|-------------|
| **.NET Security Analyzers** | NuGet package | Gratis | IDE + CI |
| **SonarQube** | Server | Community gratis | CI/CD |
| **SonarLint** | IDE extension | Gratis | VS Code, VS |
| **GitHub Code Scanning** | Integrado en GitHub | Gratis para repos públicos | CI/CD |
| **Semgrep** | CLI + CI | Gratis (OSS) | GitHub Actions |
| **Snyk** | Cloud | Free tier | CI/CD |
| **Roslyn Analyzers** | NuGet | Gratis | IDE + CI |

## CÓMO configurar en .NET

### Option 1: .NET Analyzers (más fácil)

```xml
<!-- En el .csproj -->
<ItemGroup>
  <PackageReference Include="Microsoft.CodeAnalysis.NetAnalyzers" Version="8.0.0">
    <PrivateAssets>all</PrivateAssets>
    <IncludeAssets>runtime; build; native; contentfiles; analyzers</IncludeAssets>
  </PackageReference>
</ItemGroup>
```

### Option 2: Semgrep (más potente)

```yaml
# En GitHub Actions
- name: Semgrep
  uses: returntocorp/semgrep-action@v1
  with:
    config: p/default
```

### Option 3: SonarQube (más completo)

```yaml
# En GitHub Actions
- name: SonarQube Scan
  uses: sonarsource/sonarqube-scan-action@master
  env:
    SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
    SONAR_HOST_URL: ${{ secrets.SONAR_HOST_URL }}
```

### Option 4: En CI/CD (tratar warnings como errores)

```yaml
- name: SAST
  run: dotnet build -c Release /p:TreatWarningsAsErrors=true
```

## CUÁNDO ejecutar

| Momento | Prioridad | Herramienta |
|---------|-----------|------------|
| En el IDE (tiempo real) | ✅ Alta | SonarLint, .NET Analyzers |
| Antes de commit | ✅ Alta | `dotnet format` |
| En PR (CI) | ✅ Alta | Semgrep, SonarQube, GitHub Code Scanning |
| Semanal (scheduled) | ⚠️ Media | SonarQube |

## CÓMO reducir falsos positivos

1. **Configurar reglas específicas** — no escanear todo, enfocarse en lo crítico
2. **Baseline scan** — primer scan para establecer baseline
3. **Ignorar falsos conocidos** — marcar como "won't fix"
4. **Revisar manualmente** — SAST no es perfecto, siempre verificar

## Referencia
- [[40 - RECURSOS/MOCs/MOC - Seguridad]]
- [[40 - RECURSOS/MOCs/MOC - CI-CD]]
- [[40 - RECURSOS/Conceptos/Introduccion-AppSec-DevSecOps]] — contexto de las etapas del pipeline
- [[40 - RECURSOS/Conceptos/DAST-Detallado]] — comparativa SAST vs DAST

#config #seguridad #devsecops #database #api #estudio #git #vulnerabilidades #secrets #testing #sast