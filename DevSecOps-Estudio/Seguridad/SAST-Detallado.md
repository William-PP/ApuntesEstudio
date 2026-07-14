# SAST — Static Application Security Testing (Detallado)

## QUÉ es

Análisis del **código fuente sin ejecutar** la aplicación para encontrar vulnerabilidades, code smells y malas prácticas de seguridad.

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
| **.NET Analyzers** | NuGet package | Gratis | IDE + CI |
| **SonarQube** | Server | Community gratis | CI/CD |
| **SonarLint** | IDE extension | Gratis | VS Code, VS |
| **Semgrep** | CLI + CI | Gratis (OSS) | GitHub Actions |
| **Roslyn Analyzers** | NuGet | Gratis | IDE + CI |

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

## CUÁNDO ejecutar

| Momento | Prioridad | Herramienta |
|---------|-----------|------------|
| En el IDE (tiempo real) | ✅ Alta | SonarLint, .NET Analyzers |
| Antes de commit | ✅ Alta | `dotnet format` |
| En PR (CI) | ✅ Alta | Semgrep, SonarQube |
| Semanal (scheduled) | ⚠️ Media | SonarQube |

## CÓMO reducir falsos positivos

1. **Configurar reglas específicas** — no escanear todo, enfocarse en lo crítico
2. **Baseline scan** — primer scan para establish baseline
3. **Ignorar falsos conocidos** — marcar como "won't fix"
4. **Revisar manualmente** — SAST no es perfecto, always verify

## Referencia
- [[DevSecOps-Estudio/00-Index]]
- [[Introduccion-AppSec-DevSecOps]]
- [[CI-CD/SAST]]
