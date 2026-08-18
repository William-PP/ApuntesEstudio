---
type: guía
state: activa
created: 2026-08-15
updated: 2026-08-15
tags: [config, devsecops, dotnet, api, estudio, git, user-secrets, dotnet, csharp, secrets]
aliases: [User-Secrets]
---

# Guía - User Secrets

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
- [[40 - RECURSOS/MOCs/MOC - .NET]]

#config #devsecops #dotnet #api #estudio #git #user-secrets #dotnet #csharp #secrets
