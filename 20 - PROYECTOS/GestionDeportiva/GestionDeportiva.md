---
type: proyecto
state: en-progreso
priority: alta
created: 2026-08-30
updated: 2026-08-30
related: [SIMAD-ITM, DevSecOps-Proyecto]
tags: [proyecto, gestion-deportiva, itm, deporte, base-datos, seguridad]
aliases: [GestionDeportiva, Sistema de Gestion Deportiva ITM, GestionDeportiva ITM]
---

# GestionDeportiva

> [!info] Módulo deportivo del sistema integral SIMAD-ITM
> GestionDeportiva es el módulo de gestión deportiva del Instituto Tecnológico Metropolitano de Medellín. Centraliza datos de deportistas, entrenamiento, bienestar, competencias y restricciones médicas que hoy están dispersos en hojas de cálculo, papel y memoria de los entrenadores.

## Objetivo

Construir un sistema centralizado que captura la complejidad real del entrenamiento deportivo: identidad y roles (Persona/Entrenador/Deportista), acceso y permisos (RBAC), estructura deportiva (Equipos), eventos y competencias (Torneos/Modalidades/Categorías), y gestión continua (bienestar, novedades médicas, rutinas técnicas, asistencias).

## Proyectos relacionados

Este módulo es hermano de **[[SIMAD-ITM]]**: ambos comparten la misma documentación de base (narrativa de requisitos y diccionario de entidades).

| Proyecto | Rol |
|----------|-----|
| [[SIMAD-ITM]] | Proyecto hermano — sistema integral |
| [[Flujo de un proyecto]] | DevSecOps-Proyecto (judo, proyecto de práctica) |

## Stack Tecnológico

| Capa | Tecnología | Estado |
|------|-----------|--------|
| Lenguaje | C# | Propuesto |
| Framework | .NET 10 | Propuesto |
| Base de datos | SQL Server | Propuesto |
| ORM | Entity Framework Core | Propuesto |
| Seguridad | DevSecOps (JWT, rate limiting, cifrado AES-256) | Propuesto |

## Documentación

- [[20 - PROYECTOS/GestionDeportiva/Narrativa - GestionDeportiva]] — problema, flujos, requisitos de usuario, requisitos funcionales y no funcionales, reglas de negocio
- [[20 - PROYECTOS/GestionDeportiva/Diccionario de Entidades - GestionDeportiva]] — diccionario completo del modelo relacional (5 niveles)

## Estado del proyecto

**Fase:** Diseño y modelado de datos (documentación de requisitos completa).

**Checklist:**

- [x] Narrativa del sistema (problema, causas, impacto, flujos, requisitos, reglas de negocio)
- [x] Diccionario de entidades (5 niveles: identidad, acceso, estructura, competencias, gestión continua)
- [x] Diagrama de clases UML (imagen referenciada en el diccionario)
- [ ] Diseño de API + autorización por rol
- [ ] Implementación .NET 10 + EF Core + SQL Server

## Referencia

- [[20 - PROYECTOS/SIMAD-ITM/SIMAD-ITM]] — proyecto hermano, misma base documental
- [[20 - PROYECTOS/GestionDeportiva/Narrativa - GestionDeportiva]] — requisitos del sistema
- [[20 - PROYECTOS/GestionDeportiva/Diccionario de Entidades - GestionDeportiva]] — modelo de datos
- [[Flujo de un proyecto]] — contexto original del sistema deportivo

#proyecto #gestion-deportiva #itm #deporte #seguridad
