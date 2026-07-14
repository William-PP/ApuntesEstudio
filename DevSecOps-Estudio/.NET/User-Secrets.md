# .NET — User Secrets

## QUÉ es

Mecanismo de .NET para **almacenar secrets de forma segura** durante el desarrollo local. Los secrets NO se guardan en el proyecto ni en el repositorio.

## POR QUÉ usarlo

| Sin User Secrets | Con User Secrets |
|------------------|------------------|
| Secrets en appsettings.json | Secrets fuera del proyecto |
| Commiteados al repo | Nunca en el repo |
| Visibles para todos | Solo en tu máquina |

## CÓMO usarlo

```bash
# Inicializar en el proyecto
dotnet user-secrets init --project TuProyecto.API

# Guardar un secret
dotnet user-secrets set "ConnectionStrings:DefaultConnection" "Server=..." --project TuProyecto.API

# Guardar JWT key
dotnet user-secrets set "Jwt:Key" "mi-super-secret-key-aqui" --project TuProyecto.API

# Listar todos
dotnet user-secrets list --project TuProyecto.API

# Eliminar uno
dotnet user-secrets remove "Jwt:Key" --project TuProyecto.API
```

## Dónde se guardan

| SO | Ruta |
|----|------|
| Windows | `%APPDATA%\microsoft\UserSecrets\<id>\secrets.json` |
| Linux | `~/.microsoft/usersecrets/<id>/secrets.json` |
| Mac | `~/.microsoft/usersecrets/<id>/secrets.json` |

## Convención de nombres

```
Config key:               Environment variable:
ConnectionStrings:Default  →  ConnectionStrings__Default
Jwt:Key                   →  Jwt__Key
```

Los `:` se convierten en `__` (doble underscore).

## Referencia
- [[DevSecOps-Estudio/00-Index]]
- [[DevSecOps-Proyecto/Fase-0-Seguridad/Secrets-Management]]
