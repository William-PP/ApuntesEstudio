# Trivy — Escaneo de Seguridad

## QUÉ es

Herramienta de **escaneo de vulnerabilidades** para imágenes Docker, filesystems y configuraciones IaC.

## POR QUÉ usarlo

| Sin Trivy | Con Trivy |
|-----------|----------|
| Imagenes con CVEs unknown | CVEs detectados antes de deployar |
| Vulnerabilidades en dependencias | Alertas automáticas |
| Sin visibility en superficie de ataque | Reporte completo |

## CÓMO usarlo

```bash
# Escanear imagen Docker
trivy image mi-app:latest

# Solo Critical y High
trivy image --severity CRITICAL,HIGH mi-app:latest

# Output JSON
trivy image -f json -o results.json mi-app:latest

# Escanear Dockerfile (IaC)
trivy config Dockerfile

# Escanear filesystem
trivy fs .
```

## Qué detecta

| Tipo | Ejemplo |
|------|---------|
| CVEs del OS | Vulnerabilidades en Ubuntu/Debian base |
| CVEs de packages | Vulnerabilidades en librerías NuGet/npm |
| Misconfigurations | Dockerfile inseguro, permisos abiertos |
| Secrets | API keys hardcodeadas |

## CUÁNDO ejecutar

| Momento | Automático? |
|---------|------------|
| Desarrollo local | Manual (buena práctica) |
| Push a repo | ✅ En pipeline CI |
| Pull request | ✅ En pipeline CI |
| Antes de deploy | ✅ En pipeline CD |

## Referencia
- [[DevSecOps-Estudio/00-Index]]

#config #devsecops #trivy #scanner #api #estudio #docker #git #vulnerabilidades #secrets #deploy
