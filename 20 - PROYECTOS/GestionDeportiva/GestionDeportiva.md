---
type: proyecto
state: en-progreso
priority: alta
created: 2026-08-30
updated: 2026-09-10
related: [SIMAD, DevSecOps-Proyecto]
tags: [proyecto, gestion-deportiva, simad, deporte, base-datos, seguridad]
aliases: [GestionDeportiva, Aplicación Gestión Deportiva, Gestión de Torneos Deportivos]
---

# GestionDeportiva

> [!info] Aplicación de gestión de torneos deportivos
> Aplicación para la gestión de torneos deportivos: personas, entrenadores, deportistas, equipos, torneos, inscripciones y resultados. Proyecto académico ITM (Ago–Nov 2026), evolución directa del módulo deportivo del sistema **SIMAD**.

## Objetivo

Centralizar la gestión deportiva eliminando la fragmentación de datos (papel, hojas de cálculo, memoria de entrenadores): identidad y roles (Persona/Entrenador/Deportista), estructura deportiva (Equipos), eventos y competencias (Torneos/Modalidades/Categorías), e inscripciones con resultados.

## Proyectos relacionados

| Proyecto | Rol |
|----------|-----|
| [[20 - PROYECTOS/SIMAD/SIMAD]] | Proyecto hermano — comparten narrativa y diccionario de entidades de base |
| [[20 - PROYECTOS/DevSecOps-Proyecto/Flujo de un proyecto]] | DevSecOps-Proyecto (contexto original, modelo v4) |

## Repositorio

- Repo: `https://github.com/William-PP/GestionDeportiva.git` — ramas `main` / `develop` (+ `feature/devN-*`).
- Fuente de verdad operativa: **README.md del repo** (+ `docs/GestionDeportiva-Documentacion-Completa.md`).
- Nota: el README remite a `docs/Plan_Desarrollo_Backend_GestionDeportiva.md`, **aún no creado** en el repo.

## Stack Tecnológico (según README real del repo)

| Capa | Tecnología |
|------|------------|
| API | FastAPI + Uvicorn |
| Persistencia | SQLModel (ORM) + **SQLite** |
| Configuración | pydantic-settings (`.env`) |
| Migraciones | Alembic (pendiente de configurar) |
| Lenguaje | Python 3.13 |

> [!note] Modelo de datos
> La documentación técnica describe el modelo **v4 (TPT sobre `Persona`)** que ya existe en el vault (`DevSecOps-Proyecto`). Detalle completo en [[20 - PROYECTOS/GestionDeportiva/Modelo de Datos - GestionDeportiva]].

## Documentación del proyecto

- [[20 - PROYECTOS/GestionDeportiva/Guías - GestionDeportiva]] — setup local, Git Flow, convención de commits y arquitectura por capas
- [[20 - PROYECTOS/GestionDeportiva/Modelo de Datos - GestionDeportiva]] — modelo conceptual (5 niveles) + modelo técnico v4, requisitos y seguridad

## Estado del proyecto

**Fase:** Entrega 1 completada (Análisis y Requisitos — 02/09/2026). Entrega 2 en curso (Desarrollo Backend, vence 19/10/2026): scaffold FastAPI + SQLModel listo.

**Checklist:**

- [x] Repositorio Git con Git Flow y ramas `feature/devN-*`
- [x] Scaffold de la aplicación (FastAPI + SQLModel, capas api/domain/data_access)
- [x] README del repo con puesta en marcha y workflow
- [x] Modelo de datos v4 documentado en `docs/` del repo
- [ ] Plan de desarrollo backend (`docs/Plan_Desarrollo_Backend_GestionDeportiva.md`)
- [ ] Implementación de endpoints y entidades (distribución Dev 1/2/3)
- [ ] Aplicación completa incl. frontend (Entrega 3 — 23/11/2026)

## Referencia

- [[20 - PROYECTOS/GestionDeportiva/Guías - GestionDeportiva]] — cómo levantar y trabajar con el repo
- [[20 - PROYECTOS/GestionDeportiva/Modelo de Datos - GestionDeportiva]] — modelo de datos, requisitos y seguridad
- [[20 - PROYECTOS/SIMAD/SIMAD]] — proyecto hermano, narrativa y diccionario conceptual
- [[20 - PROYECTOS/DevSecOps-Proyecto/GestionDeportiva-Diagrama-Clases]] — UML v4 + análisis de seguridad OWASP 2025

#proyecto #gestion-deportiva #simad #deporte #seguridad