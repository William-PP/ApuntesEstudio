# Fase 0 — Seguridad en Código

## Objetivo
Implementar controles de seguridad directamente en el código de la aplicación (Shift Left Level 1).

## Checklist
- [ ] Autenticación JWT
- [ ] CORS policy configurada
- [ ] Rate Limiting implementado
- [ ] Input Validation con FluentValidation
- [ ] Health Checks configurados
- [ ] Secrets fuera del código (User Secrets / env vars)

## Autenticación JWT

### ¿Qué es?
JSON Web Token — estándar para transmitir información de forma segura entre partes como un objeto JSON.

### Flujo
```
1. Usuario envía credenciales (email + password)
2. Servidor valida credenciales
3. Servidor genera JWT con claims (rol, id, exp)
4. JWT se retorna al cliente
5. Cliente envía JWT en header: Authorization: Bearer <token>
6. Servidor valida JWT en cada request
```

### Configuración
<!-- Documenta tu setup aquí -->
- **Paquete NuGet:** 
- **Issuer:** 
- **Audience:** 
- **Secret key:** (NUNCA commitear)
- **Token expiry:** 

### Comandos útiles
```bash
# Generar secret key
dotnet user-secrets set "Jwt:SecretKey" "tu-secret-aqui" --project TuProyecto.API

# Ver secrets
dotnet user-secrets list --project TuProyecto.API
```

## CORS

### ¿Qué es?
Cross-Origin Resource Sharing — controla qué dominios pueden hacer requests a tu API.

### Configuración
```csharp
// Ejemplo - adaptar a tu proyecto
builder.Services.AddCors(options =>
{
    options.AddPolicy("AllowSpecificOrigin", policy =>
    {
        policy.WithOrigins("http://localhost:3000")
              .AllowAnyHeader()
              .AllowAnyMethod();
    });
});
```

## Rate Limiting

### ¿Qué es?
Limita cuántas peticiones puede hacer un usuario en un período de tiempo. Previene abuso y DDoS.

### Configuración
```csharp
// Ejemplo - adaptar a tu proyecto
builder.Services.AddRateLimiter(options =>
{
    options.AddFixedWindowLimiter("fixed", opts =>
    {
        opts.PermitLimit = 100;
        opts.Window = TimeSpan.FromMinutes(1);
    });
});
```

## Input Validation

### ¿Qué es?
Validar que los datos que entran al sistema sean correctos y seguros. Previene inyecciones y datos maliciosos.

### Herramienta: FluentValidation
```bash
dotnet add package FluentValidation.AspNetCore
```

## Health Checks

### ¿Qué es?
Endpoints que reportan el estado de la aplicación y sus dependencias.

### Paquetes
```bash
dotnet add package AspNetCore.HealthChecks.SqlServer
dotnet add package AspNetCore.HealthChecks.UI
```

## Secrets Management

### Reglas
1. **NUNCA** commitear secrets en el repositorio
2. En **desarrollo:** usar User Secrets de .NET
3. En **producción:** usar Environment Variables o Vault

### User Secrets
```bash
# Inicializar
dotnet user-secrets init --project TuProyecto.API

# Guardar
dotnet user-secrets set "ConnectionStrings:DefaultConnection" "Server=..." --project TuProyecto.API

# Listar
dotnet user-secrets list --project TuProyecto.API
```

## Lo que aprendí en esta fase
<!-- Actualiza con tus aprendizajes -->
- 

## Links Relacionados
- [[00-Visión-General]]
- [[02-Threat-Model]]
- [[DevSecOps-Proyecto/Referencia/Glosario-DevSecOps]]
