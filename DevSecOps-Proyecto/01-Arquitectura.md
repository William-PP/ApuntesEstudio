# Arquitectura del Proyecto

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

| Capa           | Amenaza principal      | Mitigación                                   |
| -------------- | ---------------------- | -------------------------------------------- |
| API            | Inyección, XSS, CSRF   | Input validation, CORS, Rate limiting        |
| Dominio        | Lógica business bypass | Validaciones en service layer                |
| Acceso a datos | SQL Injection          | EF Core (parameterized queries)              |
| BD             | Acceso no autorizado   | Connection string seguro, mínimo privilegios |

## Tech Stack Detallado
<!-- Completa con tu stack real -->

### Backend
- **Lenguaje:** 
- **Framework:** 
- **ORM:** 

### Base de Datos
- **Motor:** 
- **Versión:** 

### Container
- **Runtime:** 
- **Orquestación:** 

### CI/CD
- **Plataforma:** 
- **Herramientas de seguridad:** 

### Cloud
- **Proveedor:** 
- **Servicios:** 

## Links Relacionados
- [[00-Visión-General]]
- [[02-Threat-Model]]
- [[03-Decisiones-Técnicas]]

## Referencia
- [[DevSecOps-Proyecto/00-Visión-General]]

#devsecops #cloud #proyecto #database #api
