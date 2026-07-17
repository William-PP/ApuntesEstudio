# Secret Scanning

## QUÉ es

Proceso de **detectar secretos** (API keys, passwords, tokens) que fueron accidentalmente commiteados al repositorio.

## POR QUÉ importa

| Sin Secret Scan | Con Secret Scan |
|-----------------|-----------------|
| Secrets en el historial de Git | Secrets detectados antes de llegar al repo |
| API keys filtradas | Alertas inmediatas |
| Brechas de seguridad | Rotación automática de secrets |

## Ejemplo real

```
// Alguien commitea esto:
var apiKey = "sk-1234567890abcdef";

Secret Scan detecta: ⚠️ API key found in file Program.cs
                     ⚠️ Line 42
                     ⚠️ GitHub will revoke this key automatically
```

## Herramientas

| Herramienta | Dónde | Costo |
|------------|-------|-------|
| **Gitleaks** | Local + CI | Gratis |
| **GitHub Secret Scanning** | Repositorios | Gratis |
| **GitLeaks Action** | GitHub Actions | Gratis |
| **TruffleHog** | Local + CI | Gratis |

## CÓMO usar Gitleaks

```bash
# Instalar
# Windows: choco install gitleaks
# Mac: brew install gitleaks

# Escanear todo el historial
gitleaks detect

# Escanear solo cambios staged
gitleaks protect --staged

# Pre-commit hook
gitleaks protect --staged --verbose
```

### En GitHub Actions
```yaml
- name: Gitleaks
  uses: gitleaks/gitleaks-action@v2
  env:
    GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

## Pre-commit hook

```bash
# Instalar pre-commit
pip install pre-commit

# .pre-commit-config.yaml
repos:
  - repo: https://github.com/gitleaks/gitleaks
    rev: v8.18.0
    hooks:
      - id: gitleaks
```

## CUÁNDO ejecutar

| Momento | Acción |
|---------|--------|
| Antes de commit | `gitleaks protect --staged` |
| Pre-commit hook | Automático |
| En CI (cada push) | GitHub Action |
| Semanal | Scheduled workflow |

## Qué detecta

| Tipo de secret | Ejemplo |
|---------------|---------|
| API keys | `sk-abc123...`, `AKIA...` |
| Passwords | `password=secret123` |
| Connection strings | `Server=...;Password=...` |
| Private keys | `-----BEGIN RSA PRIVATE KEY-----` |
| Tokens | `ghp_...`, `gho_...` |

## Referencia
- [[DevSecOps-Estudio/00-Index]]

#config #ci-cd #devsecops #api #estudio #git #secret-scanning #seguridad #secrets #sca #dependencias #github-actions
