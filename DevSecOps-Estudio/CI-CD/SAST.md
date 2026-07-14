# SAST (Static Application Security Testing)

## QUÉ es

Análisis del **código fuente sin ejecutarlo** para encontrar vulnerabilidades, code smells y malas prácticas.

## POR QUÉ importa

| Sin SAST | Con SAST |
|----------|----------|
| Bugs de seguridad encontrados en producción | Bugs encontrados al escribir código |
| Code review manual (propenso a errores) | Análisis automatizado + manual |
| Sin métricas de calidad | Reportes de deuda técnica |

## CÓMO funciona

```
Código fuente → Analizador → Reporte de vulnerabilidades
                              ├── SQL Injection
                              ├── XSS
                              ├── Hardcoded secrets
                              ├── Deprecated APIs
                              └── Code smells
```

## Herramientas para .NET

| Herramienta | Tipo | Costo |
|------------|------|-------|
| .NET Security Analyzers | NuGet package | Gratis |
| SonarQube Community | Server | Gratis |
| GitHub Code Scanning | Integrado | Gratis para repos públicos |
| Snyk | Cloud | Free tier |
| Roslyn Analyzers | NuGet | Gratis |

## CÓMO configurar en .NET

### Option 1: .NET Analyzers
```xml
<!-- En el .csproj -->
<ItemGroup>
  <PackageReference Include="Microsoft.CodeAnalysis.NetAnalyzers" Version="8.0.0">
    <PrivateAssets>all</PrivateAssets>
    <IncludeAssets>runtime; build; native; contentfiles; analyzers</IncludeAssets>
  </PackageReference>
</ItemGroup>
```

### Option 2: En CI/CD
```yaml
- name: SAST
  run: dotnet build -c Release /p:TreatWarningsAsErrors=true
```

## CUÁNDO ejecutar

| Momento | Prioridad |
|---------|-----------|
| En el IDE (tiempo real) | ✅ Alta |
| Antes de commit | ✅ Alta |
| En PR (CI) | ✅ Alta |
| Semanal (scheduled) | ⚠️ Media |

## Referencia
- [[00-Index]]
- [[CI-CD/GitHub-Actions]]
- [[CI-CD/SCA]]
