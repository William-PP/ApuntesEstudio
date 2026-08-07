# Fase 0 — Input Validation (FluentValidation)

## ¿Qué es Input Validation?

Validar que todo dato que entra al sistema (body, query params, headers) cumpla las reglas esperadas **antes** de que llegue a la lógica de negocio. No es solo formato: es la primera línea de defensa contra inputs hostiles.

> [!note] Regla de oro
> Toda entrada externa es potencialmente maliciosa. Validar temprano reduce la superficie de ataque y evita que datos malformados viajen hasta las capas profundas (BD, sistema de archivos, servicios externos).

## ¿Por qué importa?

| Sin validación | Con validación |
|---|---|
| El input llega directo a services/repos | Se bloquea en la frontera de la API |
| Datos malformados rompen la lógica de negocio | Se rechaza con un 400 claro |
| Mensajes de error raros para el cliente | Respuestas consistentes de validación |
| Mayor superficie para inyecciones | Menos input hostil procesado |

## ¿Dónde valida FluentValidation?

No es en el controller: ocurre en un **filtro de acción**, antes de ejecutar el método:

```
Request entra
    │
    ▼
Model Binding (se crea el DTO)
    │
    ▼
FluentValidation (filtro) ◄── se ejecuta ACÁ
    │
    ├── errores ──► 400 Bad Request (el controller ni se ejecuta)
    │
    └── sin errores ──► Controller → Service → Repository
```

## Setup en el proyecto

### 1. Paquetes NuGet

```bash
# En SportsLeague.API
dotnet add SportsLeague.API\SportsLeague.API.csproj package FluentValidation.AspNetCore
```

> [!info] FluentValidation.AspNetCore 11.3.1
> Trae el registro de validators (`AddValidatorsFromAssemblyContaining`) y la validación automática (`AddFluentValidationAutoValidation`). El namespace `FluentValidation.AspNetCore` es **obligatorio** para los métodos de registro.

### 2. Los 3 validators (carpeta `Validators/`)

**`UserValidator.cs`** — valida la entidad `User` (reglas de estructura del dominio):

```csharp
using FluentValidation;
using SportsLeague.Domain.Entities;

namespace SportsLeague.API.Validators;

public class UserValidator : AbstractValidator<User>
{
    public UserValidator()
    {
        RuleFor(x => x.FirstName)
            .NotEmpty().WithMessage("First name is required")
            .MaximumLength(80).WithMessage("First name must not exceed 80 characters");

        RuleFor(x => x.LastName)
            .NotEmpty().WithMessage("Last name is required")
            .MaximumLength(80).WithMessage("Last name must not exceed 80 characters");

        RuleFor(x => x.Email)
            .NotEmpty().WithMessage("Email is required")
            .EmailAddress().WithMessage("Invalid email format")
            .MaximumLength(150).WithMessage("Email must not exceed 150 characters");
    }
}
```

**`RegisterValidator.cs`** — valida `RegisterRequestDTO` (aquí SÍ van las reglas de complejidad del password):

```csharp
using FluentValidation;
using SportsLeague.API.DTOs.Request;

namespace SportsLeague.API.Validators;

public class RegisterValidator : AbstractValidator<RegisterRequestDTO>
{
    public RegisterValidator()
    {
        RuleFor(x => x.FirstName)
            .NotEmpty().WithMessage("First name is required")
            .MaximumLength(80).WithMessage("First name must not exceed 80 characters");

        RuleFor(x => x.LastName)
            .NotEmpty().WithMessage("Last name is required")
            .MaximumLength(80).WithMessage("Last name must not exceed 80 characters");

        RuleFor(x => x.Email)
            .NotEmpty().WithMessage("Email is required")
            .EmailAddress().WithMessage("Invalid email format")
            .MaximumLength(150).WithMessage("Email must not exceed 150 characters");

        RuleFor(x => x.Password)
            .NotEmpty().WithMessage("Password is required")
            .MinimumLength(6).WithMessage("Password must be at least 6 characters")
            .MaximumLength(100).WithMessage("Password must not exceed 100 characters")
            .Matches(@"[A-Z]").WithMessage("Password must contain at least one uppercase letter")
            .Matches(@"[a-z]").WithMessage("Password must contain at least one lowercase letter")
            .Matches(@"[0-9]").WithMessage("Password must contain at least one digit");
    }
}
```

**`LoginValidator.cs`** — valida `AuthRequestDTO`. Ojo, aquí NO se repite la complejidad del password:

```csharp
using FluentValidation;
using SportsLeague.API.DTOs.Request;

namespace SportsLeague.API.Validators;

public class LoginValidator : AbstractValidator<AuthRequestDTO>
{
    public LoginValidator()
    {
        RuleFor(x => x.Email)
            .NotEmpty().WithMessage("Email is required")
            .EmailAddress().WithMessage("Invalid email format");

        RuleFor(x => x.Password)
            .NotEmpty().WithMessage("Password is required");
    }
}
```

### 3. Registro en `Program.cs`

```csharp
using FluentValidation;
using FluentValidation.AspNetCore;

// ── Controllers ──
builder.Services.AddControllers();

// ── FluentValidation ──
builder.Services.AddFluentValidationAutoValidation();
builder.Services.AddValidatorsFromAssemblyContaining<Program>();
```

## Por qué LoginValidator NO repite la complejidad del password

```csharp
// ❌ MAL — validar complejidad en login
RuleFor(x => x.Password)
    .Matches(@"[A-Z]").WithMessage("Password must contain uppercase");
// Revela al atacante los requisitos del password → reduce el espacio de búsqueda en fuerza bruta

// ✅ BIEN — solo verificar que no esté vacío
RuleFor(x => x.Password)
    .NotEmpty().WithMessage("Password is required");
```

Además: un usuario registrado con reglas más laxas en el pasado no podría loguearse si el login re-valida reglas nuevas. El login solo verifica credenciales.

## Error CS1061 (lección aprendida)

```
error CS1061: "IServiceCollection" no contiene una definición para
"AddFluentValidationAutoValidation"
```

**Causa:** faltaba `using FluentValidation.AspNetCore;`. Solo `using FluentValidation;` no alcanza porque los métodos de registro de servicios viven en el namespace de ASP.NET Core integration.

## ¿Esto evita SQL Injection?

**No directamente.** FluentValidation reduce la superficie de ataque, pero el blindaje real contra SQLi es la **parameterized query**:

```csharp
// ✅ BIEN — EF Core LINQ parametriza el input automáticamente
var user = await _context.Users
    .Where(u => u.Email == email)  // Se traduce a @__email, nunca se concatena
    .FirstOrDefaultAsync();

// ❌ MAL — concatenación directa (SQL injection)
var query = $"SELECT * FROM Users WHERE Email = '{email}'";
```

Es **defense in depth**: FluentValidation rechaza inputs malformados en la frontera, y el ORM parametriza los que pasan. Ver [[DevSecOps-Estudio/Seguridad/Injection]] para el detalle de SQLi y otras inyecciones.

## Errores comunes

| Error | Causa | Solución |
|---|---|---|
| `CS1061` con `AddFluentValidationAutoValidation` | Falta `using FluentValidation.AspNetCore;` | Agregar el using |
| La validación no corre | Validator no se registró | `AddValidatorsFromAssemblyContaining<Program>()` |
| Password complejo rechazado en login | Reglas de complejidad en LoginValidator | Solo `.NotEmpty()` en login |
| Respuesta 400 sin detalle | Filtro automático activado sin configurar mensajes | Usar `.WithMessage()` en cada regla |

## Checklist

- [x] FluentValidation instalado en SportsLeague.API
- [x] `UserValidator` para la entidad `User`
- [x] `RegisterValidator` para `RegisterRequestDTO`
- [x] `LoginValidator` para `AuthRequestDTO`
- [x] `AddFluentValidationAutoValidation()` en Program.cs
- [x] `AddValidatorsFromAssemblyContaining<Program>()` en Program.cs

## Mapeo OWASP Top 10:2025

| Categoría | Relación con Input Validation |
|---|---|
| A05 Injection | Validación de inputs + parameterized queries (la mitigación real la da el ORM) |
| A10 Mishandling of Exceptional Conditions | Rechazar inputs malformados con 400 evita excepciones no manejadas que exponen detalles internos |
| A06 Insecure Design | Validación temprana forma parte de un diseño defensivo por capas |

## Links Relacionados
- [[00-Resumen-Fase-0]]
- [[JWT-Setup]]
- [[Rate-Limiting]]

## Referencia
- [[DevSecOps-Proyecto/00-Visión-General]]
- [[DevSecOps-Estudio/00-Index]]
- [[DevSecOps-Estudio/Seguridad/Injection]]
- [[DevSecOps-Proyecto/Fase-0-Seguridad/JWT-Setup]]
- [[DevSecOps-Proyecto/Fase-0-Seguridad/Rate-Limiting]]
- [[DevSecOps-Proyecto/Fase-0-Seguridad/CORS-Config]]
- [[DevSecOps-Proyecto/Referencia/Glosario-DevSecOps]]

#seguridad #devsecops #proyecto #api #fase-0 #input-validation #fluentvalidation #owasp
