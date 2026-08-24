---
type: concepto
state: activa
created: 2026-08-15
updated: 2026-08-23
tags: [hardening, ciberseguridad, nist, cis, inventario, clasificacion, gobernanza]
aliases: [Hardening Fase 1]
---

# Hardening Fase 1: Identificación, Inventario y Planificación

La **Fase 1** es el pilar fundamental: no es posible proteger activos que no han sido debidamente identificados. Basado en los marcos de **NIST** y **CIS**, esta etapa se desglosa en las siguientes acciones.

## Marcos de referencia

| Marco | Enfoque | Cómo se usa acá |
|-------|---------|-----------------|
| **CIS Controls v8.1** | 18 controles críticos de seguridad prioritarios | Base de higiene esencial (IG1): parches, contraseñas, firewall, backups |
| **Guía de Hardening de Linux (Madaidan's)** | Guía técnica agnóstica a la distribución | Detalle técnico de las configuraciones de las Fases 3-5 |
| **NIST CSF 2.0** | Gobernar, Identificar, Proteger, Detectar, Responder, Recuperar | Estructura el proceso completo |
| **NIST SP 800-53 Rev. 5** | Catálogo exhaustivo de controles | Referencia formal aplicable a cada fase |

## 1. Inventario de Activos de Hardware

Registrar y mantener un inventario detallado de todos los activos físicos con potencial para almacenar o procesar datos.

| Acción | Detalle |
|--------|---------|
| **Identificación completa** | Registrar dispositivos finales (portátiles, móviles), dispositivos de red, servidores y IoT |
| **Datos del registro** | Dirección de red (si es estática), MAC, nombre de máquina y propietario |
| **Control de componentes** | NIST SP 800-53 (CM-8): el inventario debe reflejar con precisión el sistema para evitar componentes no asignados como vulnerabilidades |

## 2. Inventario y Control de Software

Gestionar activamente el SO y todas las aplicaciones instaladas.

| Acción | Detalle |
|--------|---------|
| **Documentación de licencias** | Título, editor, fecha de instalación y propósito de negocio |
| **Gestión del ciclo de vida** | NIST recomienda inventarios para priorizar según importancia (ID.AM-02) |
| **Eliminación de software no autorizado** | Remover programas no permitidos o documentar excepciones mensualmente |

## 3. Clasificación y Gestión de Datos

Identificar y priorizar la sensibilidad de la información almacenada.

| Acción | Detalle |
|--------|---------|
| **Esquema de clasificación** | Etiquetas como "Pública", "Confidencial" o "Sensible" |
| **Mapeo de datos** | Qué apps acceden a qué niveles de sensibilidad y en qué activos residen |
| **Categorización de seguridad** | NIST SP 800-53 (RA-2): prever el impacto de comprometer confidencialidad, integridad o disponibilidad |

## 4. Análisis de Riesgos y Modelado de Amenazas

Comprender el contexto de ciberseguridad específico de tu sistema.

| Acción | Detalle |
|--------|---------|
| **Identificación de amenazas** | Evaluar vulnerabilidades de activos e impactos potenciales |
| **Modelado técnico** | Análisis de vectores de ataque e identificación de controles compensatorios |

## 5. Gobernanza y Políticas

La función **GOBERNAR (GV)** de NIST CSF 2.0: la estrategia de gestión de riesgos debe estar documentada y comunicada.

| Acción | Detalle |
|--------|---------|
| **Proceso de configuración segura** | Establecer y mantener un proceso documentado (CIS Control 4.1) |
| **Selección de la línea base** | Elegir benchmarks predefinidos como punto de partida |

> [!note] Resultado
> Al finalizar, habrás reducido la incertidumbre y tendrás una **hoja de ruta clara**: la seguridad es, ante todo, un problema de diseño y planificación.

> [!tip] Observación
> La parte más ignorada es el **Inventario de Activos y Clasificación de Datos** (CIS Controles 1, 2 y 3). Si no sabés qué software tenés o qué datos son sensibles, aplicarás el mismo esfuerzo a cosas triviales que a cosas críticas.

---

## Referencia
- [[40 - RECURSOS/Conceptos/Hardening]]
- [[40 - RECURSOS/MOCs/MOC - Hardening]]
- [[40 - RECURSOS/MOCs/MOC - Ciberseguridad]]

#hardening #nist #cis #inventario #clasificacion #gobernanza
