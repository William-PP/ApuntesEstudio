# Fase 0 — Secrets Management

## ¿Qué son Secrets?

Credenciales, API keys, connection strings, y cualquier dato que no debería ser visible públicamente.

### Regla de oro
> **NUNCA** commitear secretos al repositorio. Ni en código, ni en appsettings.json, ni en ningún archivo versionado.

## Niveles de Secrets Management

| Nivel | Herramienta | Cuándo usar |
|-------|------------|-------------|
| **Desarrollo** | .NET User Secrets | Local, tu máquina |
| **CI/CD** | GitHub Secrets | En el pipeline |
| **Producción** | Environment Variables / Azure Key Vault / AWS Secrets Manager | Servidor real |

## .NET User Secrets (Desarrollo)

```bash
# Inicializar en el proyecto
dotnet user-secrets init --project TuProyecto.API

# Guardar un secret
dotnet user-secrets set "ConnectionStrings:DefaultConnection" "Server=localhost;Database=MiDB;..." --project TuProyecto.API

# Guardar JWT key
dotnet user-secrets set "Jwt:Key" "mi-super-secret-key-aqui" --project TuProyecto.API

# Listar todos
dotnet user-secrets list --project TuProyecto.API

# Eliminar
dotnet user-secrets remove "Jwt:Key" --project TuProyecto.API
```

### Dónde se almacenan
- **Windows:** `%APPDATA%\microsoft\UserSecrets\<user-secrets-id>\secrets.json`
- **Linux:** `~/.microsoft/usersecrets/<user-secrets-id>/secrets.json`

## appsettings.json (SIN secretos)

```json
{
  "ConnectionStrings": {
    "DefaultConnection": ""  // Vacío, se llena con User Secrets o env vars
  },
  "Jwt": {
    "Key": "",
    "Issuer": "TuApp",
    "Audience": "TuApp"
  }
}
```

## Environment Variables (Producción)

```bash
# Linux/Mac
export ConnectionStrings__DefaultConnection="Server=..."
export Jwt__Key="mi-secret-key"

# Windows PowerShell
$env:ConnectionStrings__DefaultConnection="Server=..."
$env:Jwt__Key="mi-secret-key"
```

### Convención de nombres
```
ConnectionStrings:DefaultConnection  →  ConnectionStrings__DefaultConnection
Jwt:Key                              →  Jwt__Key
```
Los `:` se convierten en `__` (doble underscore).

## GitHub Secrets (CI/CD)

```bash
# En GitHub → Settings → Secrets and variables → Actions → New repository secret
# Nombre: AZURE_CREDENTIALS
# Valor: { JSON del service principal }
```

## .gitignore para secrets

```gitignore
# User Secrets (ya debería estar)
**/secrets.json

# Environment files
.env
.env.local
.env.production

# appsettings con secretos
appsettings.*.local.json
```

## Errores comunes
| Error | Causa | Solución |
|-------|-------|----------|
| `NullReferenceException` en config | Secret no encontrado | Verificar User Secrets o env var |
| `Invalid key length` | JWT key muy corta | Mínimo 32 caracteres |
| Secret en el repo | Alguien lo commiteó | Rotar el secret inmediatamente |

## Checklist de seguridad
- [ ] No hay secretos en appsettings.json
- [ ] No hay secretos en el código fuente
- [ ] User Secrets configurado para desarrollo
- [ ] .gitignore excluye archivos sensibles
- [ ] GitHub Secrets configurado para CI/CD
- [ ] Environment Variables en el servidor de producción

## Notas personales
- 

## Links Relacionados
- [[00-Resumen-Fase-0]]
- [[DevSecOps-Estudio/.NET/Health-Checks]]
- [[JWT-Setup]]

## Referencia
- [[DevSecOps-Proyecto/00-Visión-General]]
- [[DevSecOps-Estudio/00-Index]]
- [[DevSecOps-Proyecto/Fase-0-Seguridad/Health-Checks]]
- [[DevSecOps-Proyecto/Fase-0-Seguridad/Rate-Limiting]]
- [[DevSecOps-Proyecto/Fase-0-Seguridad/JWT-Setup]]

#config #seguridad #devsecops #cloud #database #proyecto #api #git #secrets #fase-0 #checklist
