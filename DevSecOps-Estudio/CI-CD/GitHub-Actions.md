# GitHub Actions — CI/CD

## QUÉ es

Plataforma de **CI/CD integrada en GitHub** que ejecuta workflows automatizados cada vez que ocurre un evento en el repo.

## Conceptos

| Concepto | Definición |
|----------|-----------|
| **Workflow** | Archivo YAML que define el pipeline |
| **Job** | Conjunto de steps que corren en la misma máquina |
| **Step** | Acción individual (run command, use action) |
| **Action** | Componente reutilizable (actions/checkout@v4) |
| **Trigger** | Evento que inicia el workflow (push, PR, schedule) |

## CÓMO funciona

```
Push/PR → GitHub Actions → Lee .github/workflows/ci.yml → Ejecuta jobs
```

## Template básico

```yaml
name: CI Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  build-and-test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-dotnet@v4
        with:
          dotnet-version: '10.0.x'
      - run: dotnet restore
      - run: dotnet build --no-restore
      - run: dotnet test
```

## CUÁNDO usar

| Evento | Workflow típico |
|--------|----------------|
| `push` a main | Build + Test + Deploy |
| `push` a develop | Build + Test |
| `pull_request` | Build + Test + SAST + SCA |
| `schedule` | Escaneo de vulnerabilidades |
| `workflow_dispatch` | Deploy manual |

## DÓNDE configurar

```
.github/
└── workflows/
    ├── ci.yml          # Build + Test + Security
    ├── cd.yml          # Deploy a producción
    └── security.yml    # Escaneo semanal
```

## Referencia
- [[00-Index]]
- [[CI-CD/SAST]]
- [[CI-CD/SCA]]
- [[DevSecOps-Proyecto/Fase-3-CICD/00-Resumen-Fase-3]]
