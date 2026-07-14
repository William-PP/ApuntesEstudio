# Fase 3 — CI/CD con GitHub Actions

## Objetivo
Automatizar build, test, análisis de seguridad y despliegue con un pipeline completo.

## Pipeline DevSecOps

```
┌─────────────────────────────────────────────────────────────────────────┐
│                        GitHub Actions Pipeline                         │
│                                                                         │
│   ┌─────────┐   ┌─────────┐   ┌─────────┐   ┌─────────┐             │
│   │  BUILD  │──▶│  TEST   │──▶│  SAST   │──▶│  SCA    │             │
│   │dotnet   │   │dotnet   │   │.NET     │   │NuGet    │             │
│   │build    │   │test     │   │Analyzer │   │audit    │             │
│   └─────────┘   └─────────┘   └─────────┘   └─────────┘             │
│        │                                            │                  │
│        │              ┌─────────┐   ┌─────────┐    │                  │
│        │              │SECRET   │◀──│DOCKER   │◀───┘                  │
│        │              │SCAN     │   │BUILD+   │                       │
│        │              │Gitleaks │   │TRIVY    │                       │
│        │              └─────────┘   └────┬────┘                       │
│        │                                 │                            │
│        │              ┌─────────┐        │                            │
│        └─────────────▶│ DEPLOY  │◀───────┘                            │
│                       │         │                                     │
│                       └─────────┘                                     │
└─────────────────────────────────────────────────────────────────────────┘
```

## Archivo: `.github/workflows/ci.yml`

```yaml
name: DevSecOps Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  # ═══════════════════════════════════════════════════
  # STAGE 1: BUILD
  # ═══════════════════════════════════════════════════
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup .NET
        uses: actions/setup-dotnet@v4
        with:
          dotnet-version: '10.0.x'

      - name: Restore dependencies
        run: dotnet restore

      - name: Build
        run: dotnet build --no-restore -c Release

  # ═══════════════════════════════════════════════════
  # STAGE 2: TEST
  # ═══════════════════════════════════════════════════
  test:
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup .NET
        uses: actions/setup-dotnet@v4
        with:
          dotnet-version: '10.0.x'

      - name: Test
        run: dotnet test -c Release --verbosity normal /p:CollectCoverage=true /p:CoverletOutputFormat=cobertura

      - name: Upload coverage
        uses: actions/upload-artifact@v4
        with:
          name: coverage
          path: '**/coverage.cobertura.xml'

  # ═══════════════════════════════════════════════════
  # STAGE 3: SAST (Static Application Security Testing)
  # ═══════════════════════════════════════════════════
  sast:
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup .NET
        uses: actions/setup-dotnet@v4
        with:
          dotnet-version: '10.0.x'

      - name: Restore
        run: dotnet restore

      - name: Build (activa analyzers)
        run: dotnet build -c Release /p:TreatWarningsAsErrors=true

      - name: Format check
        run: dotnet format --verify-no-changes --no-restore

  # ═══════════════════════════════════════════════════
  # STAGE 4: SCA (Software Composition Analysis)
  # ═══════════════════════════════════════════════════
  sca:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup .NET
        uses: actions/setup-dotnet@v4
        with:
          dotnet-version: '10.0.x'

      - name: Restore
        run: dotnet restore

      - name: Check vulnerable packages
        run: |
          dotnet list package --vulnerable --include-transitive > vulnerable.txt
          if grep -q "has the following vulnerable packages" vulnerable.txt; then
            echo "❌ Vulnerable packages found!"
            cat vulnerable.txt
            exit 1
          fi
          echo "✅ No vulnerable packages"

  # ═══════════════════════════════════════════════════
  # STAGE 5: SECRET SCAN
  # ═══════════════════════════════════════════════════
  secret-scan:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Gitleaks
        uses: gitleaks/gitleaks-action@v2
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

  # ═══════════════════════════════════════════════════
  # STAGE 6: DOCKER BUILD + SCAN
  # ═══════════════════════════════════════════════════
  docker:
    runs-on: ubuntu-latest
    needs: [test, sast, sca, secret-scan]
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Build Docker image
        run: docker build -t tu-app:${{ github.sha }} .

      - name: Trivy scan
        uses: aquasecurity/trivy-action@master
        with:
          image-ref: 'tu-app:${{ github.sha }}'
          format: 'table'
          exit-code: '1'
          severity: 'CRITICAL,HIGH'

  # ═══════════════════════════════════════════════════
  # STAGE 7: DEPLOY
  # ═══════════════════════════════════════════════════
  deploy:
    runs-on: ubuntu-latest
    needs: [docker]
    if: github.ref == 'refs/heads/main'
    environment: production
    steps:
      - name: Deploy
        run: echo "🚀 Deploying to production..."
```

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
    labels:
      - "dependencies"
      - "security"

  - package-ecosystem: "github-actions"
    directory: "/"
    schedule:
      interval: "weekly"
```

## Quality Gates

| Gate | Condición | Si falla |
|------|-----------|----------|
| Build | `dotnet build` exitoso | Pipeline falla |
| Tests | Todos pasan | Pipeline falla |
| Coverage | > 70% | Advertencia |
| SAST | Sin errores de analyzers | Pipeline falla |
| SCA | Sin CVEs Critical/High | Pipeline falla |
| Secret Scan | Sin secretos detectados | Pipeline falla |
| Docker Scan | Sin CVEs Critical/High | Pipeline falla |

## Notas personales
- 

## Links Relacionados
- [[00-Resumen-Fase-0]]
- [[DevSecOps-Proyecto/Referencia/Glosario-DevSecOps]]
