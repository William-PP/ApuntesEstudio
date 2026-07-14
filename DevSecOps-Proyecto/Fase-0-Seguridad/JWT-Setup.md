# Fase 0 — JWT Setup

## ¿Qué es JWT?

## Flujo de Autenticación

```
┌──────────┐         ┌──────────┐         ┌──────────┐
│  Cliente  │────────▶│  API     │────────▶│   BD     │
│          │ login   │          │ query   │          │
│          │ email+  │ Valida   │         │          │
│          │ pass    │ creden-  │         │          │
│          │◀────────│ ticiales │◀────────│          │
│          │  JWT    │          │ user    │          │
│          │         │ Genera   │         │          │
│          │         │ JWT      │         │          │
└──────────┘         └──────────┘         └──────────┘

┌──────────┐         ┌──────────┐
│  Cliente  │────────▶│  API     │
│          │ request │          │
│          │ header: │ Valida   │
│          │ Bearer  │ JWT      │
│          │ <token> │          │
│          │◀────────│ Responde │
└──────────┘         └──────────┘
```

## Configuración en .NET

### Paquetes NuGet
```bash
dotnet add package Microsoft.AspNetCore.Authentication.JwtBearer
```

### appsettings.json ( Development — User Secrets en producción)
```json
{
  "Jwt": {
    "Key": "AQUÍ_SECRET_KEY",
    "Issuer": "TuApp",
    "Audience": "TuApp",
    "ExpiryInMinutes": 60
  }
}
```

### Program.cs
```csharp
// Agregar autenticación
builder.Services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(options =>
    {
        options.TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = true,
            ValidateAudience = true,
            ValidateLifetime = true,
            ValidateIssuerSigningKey = true,
            ValidIssuer = builder.Configuration["Jwt:Issuer"],
            ValidAudience = builder.Configuration["Jwt:Audience"],
            IssuerSigningKey = new SymmetricSecurityKey(
                Encoding.UTF8.GetBytes(builder.Configuration["Jwt:Key"]))
        };
    });

// Agregar autorización
builder.Services.AddAuthorization();

// En el pipeline (ANTES de MapControllers)
app.UseAuthentication();
app.UseAuthorization();
```

### Controller de Login
```csharp
[HttpPost("login")]
public async Task<IActionResult> Login([FromBody] LoginDTO dto)
{
    // 1. Validar credenciales contra BD
    // 2. Si son correctas, generar JWT
    // 3. Retornar token
}
```

## Claims comunes en el JWT
| Claim | Propósito |
|-------|-----------|
| `sub` | ID del usuario |
| `name` | Nombre del usuario |
| `role` | Rol (Admin, User, etc.) |
| `exp` | Expiración del token |
| `iat` | Fecha de emisión |

## Errores comunes
| Error | Causa | Solución |
|-------|-------|----------|
| `401 Unauthorized` | Token no enviado o inválido | Verificar header Authorization |
| `403 Forbidden` | Token válido pero sin permisos | Verificar role claims |
| `SecurityTokenExpiredException` | Token expirado | Implementar refresh tokens |

## Notas personales
<!-- Agrega tus aprendizajes y problemas encontrados -->
- 

## Links Relacionados
- [[00-Resumen-Fase-0]]
- [[DevSecOps-Proyecto/Referencia/Glosario-DevSecOps]]
