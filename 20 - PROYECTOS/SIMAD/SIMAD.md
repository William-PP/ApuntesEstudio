---
type: proyecto
state: en-progreso
priority: alta
created: 2026-08-30
updated: 2026-09-05
related: [GestionDeportiva, DevSecOps-Proyecto]
tags: [proyecto, simad, sistema-informacion, deporte, seguridad]
aliases: [SIMAD, Sistema Integral de Gestión Deportiva]
---

# SIMAD

> [!info] Sistema Integral de Gestión Deportiva
> SIMAD es el sistema de información integral del proyecto. Su módulo deportivo — **GestionDeportiva** — es el foco actual de documentación, y comparte con este proyecto la misma base documental (narrativa de requisitos y diccionario de entidades).

## Objetivo

Proveer una plataforma centralizada que elimine la fragmentación de datos (papel, hojas de cálculo, memoria de entrenadores) y garantice trazabilidad, auditoría y cumplimiento legal (Ley 1581 de Protección de Datos Personales).

## Proyectos relacionados

Compuesto por el módulo deportivo **[[GestionDeportiva]]**, proyecto hermano con el que comparte documentación.

| Proyecto | Rol |
|----------|-----|
| [[GestionDeportiva]] | Módulo deportivo — mismo dominio y documentación |

## Módulos

| Módulo | Descripción |
|--------|-------------|
| [[GestionDeportiva]] | Gestión de deportistas, entrenamiento, bienestar, competencias y restricciones médicas |

## Handbook del proyecto

- [[20 - PROYECTOS/SIMAD/Handbook - SIMAD]] — plantilla de las 8 etapas del proyecto, personalizable por el equipo

## Etapa 1 — Definición y Planificación Inicial

Documentación de la primera etapa del ciclo de vida (según la [[20 - PROYECTOS/SIMAD/Handbook - SIMAD]]):

- [[20 - PROYECTOS/SIMAD/Etapa 1/Acta de Constitución - SIMAD]] — charter, alcance, objetivos, interesados, criterios de éxito y riesgos
- [[20 - PROYECTOS/SIMAD/Etapa 1/Stack Tecnológico - SIMAD]] — decisiones técnicas iniciales (stack, cloud, autenticación, seguridad)
- [[20 - PROYECTOS/SIMAD/Etapa 1/Seguridad Inicial - SIMAD]] — clasificación de datos, regulaciones, amenazas y política de secretos
- [[20 - PROYECTOS/SIMAD/Etapa 1/Narrativa - SIMAD]] — problema, flujos, requisitos de usuario, requisitos funcionales y no funcionales, reglas de negocio

## Documentación del modelo de datos

- [[20 - PROYECTOS/SIMAD/Diccionario de Entidades - SIMAD]] — diccionario completo del modelo relacional (5 niveles), con diagrama de clases UML

## Estado del proyecto

**Fase:** Etapa 1 — Diseño y modelado de datos (documentación de requisitos completa).

**Checklist:**

- [x] Acta de constitución (charter, alcance, riesgos, éxito)
- [x] Stack tecnológico decidido y documentado
- [x] Seguridad inicial (datos, regulaciones, amenazas, secretos)
- [x] Narrativa del sistema (problema, causas, impacto, flujos, requisitos, reglas de negocio)
- [x] Diccionario de entidades (5 niveles: identidad, acceso, estructura, competencias, gestión continua)
- [ ] Etapa 2 — Análisis de viabilidad (RF/RNF formales, threat modeling STRIDE)
- [ ] Etapa 3 — Diseño y arquitectura (diagrama de arquitectura integral)
- [ ] Cumplimiento Ley 1581 (cifrado, auditoría, retención)

## Referencia

- [[20 - PROYECTOS/GestionDeportiva/GestionDeportiva]] — módulo deportivo, misma base documental
- [[Guía - Documentación de Proyectos de Software]] — plantilla de las 8 etapas del proyecto
- [[20 - PROYECTOS/DevSecOps-Proyecto/Flujo de un proyecto]] — contexto original del sistema deportivo

#proyecto #simad #sistema-informacion #deporte #seguridad