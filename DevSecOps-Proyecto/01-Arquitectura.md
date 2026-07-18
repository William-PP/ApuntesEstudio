---
tags: [proyecto/gestion-deportiva, devsecops, arquitectura]
created: 2026-07-14
updated: 2026-07-18
---

# Arquitectura del Proyecto

> Este archivo describe **cómo está estructurado** el sistema. Para ver **por qué** se eligió cada tecnología, ir a [[DevSecOps-Proyecto/03-Decisiones-Técnicas]].

## Diagrama de Arquitectura

```
┌─────────────────────────────────────────────────┐
│                   CLIENTE                       │
│              (Navegador/App)                    │
└─────────────────────┬───────────────────────────┘
                      │ HTTP/HTTPS
                      ▼
┌─────────────────────────────────────────────────┐
│              API (Presentación)                 │
│         Controllers / DTOs / Middleware         │
│  ┌──────────┐ ┌──────────┐ ┌──────────────────┐│
│  │JWT Auth  │ │CORS      │ │Rate Limiting     ││
│  └──────────┘ └──────────┘ └──────────────────┘│
└─────────────────────┬───────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────┐
│            DOMINIO (Lógica de Negocio)          │
│           Services / Interfaces / Entities      │
└─────────────────────┬───────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────┐
│         ACCESO A DATOS (Persistencia)           │
│       DbContext / Repositories / Migrations     │
└─────────────────────┬───────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────┐
│              BASE DE DATOS                      │
│                 SQL Server                      │
└─────────────────────────────────────────────────┘
```

## Capas y Responsabilidades

### API (Presentación)
- Recibe requests HTTP
- Valida inputs (DTOs + FluentValidation)
- Autentica usuarios (JWT)
- Retorna responses (status codes apropiados)
- **NO** contiene lógica de negocio

### Dominio (Lógica de Negocio)
- Contiene la lógica pura de la aplicación
- Define interfaces (contracts)
- Entidades y Enums
- Services con la lógica de reglas de negocio
- **NO** depende de infraestructura

### Acceso a Datos (Persistencia)
- Implementa interfaces del Dominio
- DbContext con Entity Framework
- Repositories para acceso a BD
- Migrations
- Seeders

## Principios de Seguridad por Capa

| Capa | Amenaza principal | Mitigación |
|------|------------------|------------|
| API | Inyección, XSS, CSRF | Input validation, CORS, Rate limiting |
| Dominio | Lógica business bypass | Validaciones en service layer |
| Acceso a datos | SQL Injection | EF Core (parameterized queries) |
| BD | Acceso no autorizado | Connection string seguro, mínimo privilegios |

## Tech Stack (resumen)

> Para el análisis completo de pros/contras de cada tecnología, ver [[DevSecOps-Proyecto/03-Decisiones-Técnicas]].

| Capa | Tecnología | Versión |
|------|-----------|---------|
| Lenguaje | C# | .NET 10 |
| Framework | ASP.NET Core Web API | .NET 10 |
| ORM | Entity Framework Core | 8.0.* |
| Base de datos | SQL Server | — |
| Container | Docker + Docker Compose | — |
| CI/CD | GitHub Actions | — |
| SAST | SonarCloud | — |
| SCA | Trivy | — |
| Secret Scanning | Gitleaks | — |
| DAST | OWASP ZAP | — |
| Monitoreo | Prometheus + Grafana | — |

## Dependencias entre proyectos

```
GestionDeportiva.API ──→ GestionDeportiva.Domain ←── GestionDeportiva.DataAccess
```

- **API** depende de Domain (usa entidades e interfaces)
- **DataAccess** depende de Domain (implementa interfaces)
- **Domain** NO depende de nadie (capa central, pura)

## Estructura de carpetas

```
GestionDeportiva/
├── GestionDeportiva.sln
├── .gitignore
│
├── GestionDeportiva.API/              ← Presentación (HTTP)
│   ├── Controllers/                   ← Endpoints REST
│   ├── DTOs/
│   │   ├── Request/                   ← Modelos de entrada
│   │   └── Response/                  ← Modelos de salida
│   ├── Mappings/                      ← Conversión entidades ↔ DTOs
│   ├── Middleware/                     ← Componentes de pipeline HTTP
│   └── Program.cs                     ← Punto de entrada, DI, middleware
│
├── GestionDeportiva.Domain/           ← Lógica de negocio (pura)
│   ├── Entities/                      ← Modelos de dominio
│   ├── Enums/                         ← Tipos enumerados
│   ├── Interfaces/
│   │   ├── Repositories/              ← Contratos de persistencia
│   │   └── Services/                  ← Contratos de lógica
│   └── Services/                      ← Implementaciones de lógica
│
└── GestionDeportiva.DataAccess/       ← Persistencia (EF Core)
    ├── Context/                       ← DbContext + Fluent API
    ├── Repositories/                  ← Implementaciones de repositories
    ├── Seeders/                       ← Datos iniciales
    └── Migrations/                    ← Migraciones EF Core
```

### Por qué esta estructura y no otra

| Alternativa descartada | Por qué no se usa |
|------------------------|-------------------|
| **Proyecto único** (todo junto) | Mezcla presentación, negocio y persistencia en un solo proyecto. Cambiar la BD obliga a tocar código de la API. No se puede testear la lógica de negocio sin levantar la API. |
| **Clean Architecture** (4+ capas) | Más compleja de lo que este proyecto necesita. Project References anillados, abstractions excesivas. Para una app CRUD de gestión deportiva es over-engineering. |
| **Carpeta `Features/`** (vertical slices) | Organiza por funcionalidad en vez de por capa. Es un patrón válido pero menos común en .NET, harder de encontrar tutoriales y documentación. |
| **Carpeta `Common/` o `Shared/`** | Tiende a convertirse en un cajón de sastre donde se mete todo. En esta arquitectura, lo "compartido" vive en Domain (interfaces, entidades). |

### Por qué cada carpeta existe

| Carpeta | Existe porque | Si la quitás |
|---------|--------------|--------------|
| `Controllers/` | Separa la capa HTTP de la lógica. Cada controller es un endpoint. | Los endpoints vivirían en Program.cs (solo viable para apps de 2 endpoints). |
| `DTOs/Request/` | El modelo de entrada al sistema NO debe ser la entidad de dominio. Un atacante podría enviar campos que no debería modificar. | Exposición de campos internos; imposible validar solo lo que el usuario debe enviar. |
| `DTOs/Response/` | El modelo de salida NO debe devolver todo de la entidad. `numero_documento` nunca debe salir en respuestas. | Fuga de datos sensibles (cédulas, emails internos). |
| `Mappings/` | Centraliza la conversión entidades ↔ DTOs en un solo lugar. | Lógica de mapeo repetida en cada controller, propensa a errores. |
| `Middleware/` | Componentes que procesan requests antes/después de los controllers (ej. manejo de errores global). | Manejo de errores duplicado en cada action, inconsistente. |
| `Entities/` | El corazón del dominio. Todo lo demás gira alrededor de estas clases. | No hay contrato de qué datos maneja el sistema. |
| `Enums/` | Evita "magic strings" en el código. `EstadoTorneo.INSCRIPCIONES_ABIERTAS` vs `"INSCRIPCIONES_ABIERTAS"`. | Strings sueltos por todo el código, errores en runtime por typos. |
| `Interfaces/` | Permite cambiar implementaciones sin tocar el Dominio. Hoy usa SQL Server, mañana podría usar MongoDB. | Acoplamiento directo a SQL Server en toda la lógica de negocio. |
| `Services/` | Implementa la lógica de negocio. Separada de controllers para poder testearse sin HTTP. | Lógica de negocio en controllers = imposible unit testear. |
| `Context/` | Un solo lugar donde está configurado el modelo de EF Core (Fluent API). | Configuración esparcida o por data annotations (menos control). |
| `Repositories/` | Abstrae el acceso a BD. Si mañana cambia el ORM, solo se cambia esta capa. | Lógica de LINQ/SQL dispersa en services. |
| `Seeders/` | Datos de prueba reproducibles. Ejecutar una vez y tener la BD poblada. | Cada desarrollador crea datos a mano, inconsistencia entre entornos. |
| `Migrations/` | Historial de cambios en el esquema de BD. Permite subir/bajar versiones. | Cada persona crea la BD de forma distroma, imposible reproducir el esquema. |

### Seguridad de la estructura

| Decisión de estructura | Protección contra |
|------------------------|-------------------|
| DTOs separados de Entities | Mass assignment (un atacante envía `id_usuario` en el body y se asigna a sí mismo un rol) |
| Interfaces en Domain | Tight coupling (cambiar la BD obliga a reescribir toda la lógica) |
| Domain sin dependencias | Circular references (que Domain dependa de DataAccess crearía un cycle) |
| Middleware separado | Error handling inconsistente (cada controller maneja errores de forma distinta) |

## Referencia
- [[DevSecOps-Proyecto/00-Visión-General]]
- [[DevSecOps-Proyecto/02-Threat-Model]]
- [[DevSecOps-Proyecto/03-Decisiones-Técnicas]]

---
#devsecops #arquitectura #proyecto #gestion-deportiva
