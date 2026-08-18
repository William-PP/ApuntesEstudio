---
type: tarea
state: en-progreso
created: 2026-08-15
updated: 2026-08-15
tags: [config, seguridad, devsecops, proyecto, api, secrets, fase-0, cors, seguridad-web]
---

# Fase 0 — CORS Config

## ¿Qué es CORS?

**Cross-Origin Resource Sharing** — mecanismo que usan los navegadores para controlar qué dominios pueden acceder a los recursos de tu API.

### ¿Por qué existe?
Por seguridad. Sin CORS, cualquier sitio web podría hacer requests a tu API usando las credenciales del usuario.

### Ejemplo
```
Tu app web (localhost:3000) → API (localhost:5000)  ✅ Permitido
Sitio malicioso (evil.com) → API (localhost:5000)  ❌ Bloqueado
```

## Configuración en .NET

```csharp
// En Program.cs
builder.Services.AddCors(options =>
{
    options.AddPolicy("AllowFrontend", policy =>
    {
        policy.WithOrigins(
                "http://localhost:3000",   // Frontend local
                "https://tudominio.com"    // Frontend producción
              )
              .AllowAnyHeader()
              .AllowAnyMethod()
              .AllowCredentials();  // Si usas cookies/auth
    });
});

// En el pipeline
app.UseCors("AllowFrontend");
```

## Políticas de CORS

| Política | Uso | Seguridad |
|----------|-----|-----------|
| `AllowAnyOrigin` | Solo desarrollo | ❌ Inseguro |
| `WithOrigins` | Producción | ✅ Seguro |
| `AllowAnyHeader` | Desarrollo | ⚠️ Moderado |
| `WithHeaders` | Producción | ✅ Seguro |

## Headers importantes

| Header | Propósito |
|--------|-----------|
| `Access-Control-Allow-Origin` | Dominios permitidos |
| `Access-Control-Allow-Methods` | Métodos HTTP permitidos |
| `Access-Control-Allow-Headers` | Headers permitidos |
| `Access-Control-Allow-Credentials` | Si envía cookies |

## Errores comunes
| Error | Causa | Solución |
|-------|-------|----------|
| `CORS error` en navegador | Origin no está en la lista | Agregar origin a WithOrigins |
| `Preflight failed` | Método/header no permitido | Configurar AllowAnyMethod/Header |

## Notas personales
- 

## Links Relacionados
- [[Seguridad del codigo]]
- [[JWT-Setup]]

## Referencia
- [[20 - PROYECTOS/DevSecOps-Proyecto/Fase-0-Seguridad/Fase-0-Seguridad]]
- [[Flujo de un proyecto]]
- [[20 - PROYECTOS/DevSecOps-Proyecto/Fase-0-Seguridad/Health-Checks-Proyecto]]
- [[20 - PROYECTOS/DevSecOps-Proyecto/Fase-0-Seguridad/Rate-Limiting-Proyecto]]
- [[20 - PROYECTOS/DevSecOps-Proyecto/Fase-0-Seguridad/JWT-Setup]]

#config #seguridad #devsecops #proyecto #api #secrets #fase-0 #cors #seguridad-web
