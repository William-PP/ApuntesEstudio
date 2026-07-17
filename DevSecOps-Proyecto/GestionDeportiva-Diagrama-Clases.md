---
tags: [proyecto/gestion-deportiva, uml, diagrama-clases, devsecops]
created: 2026-07-15
updated: 2026-07-15
---

# GestionDeportiva — Diagrama de Clases

> Diagrama UML del modelo de dominio. Complementa el diccionario de datos ([[DevSecOps-Proyecto/GestionDeportiva-Diccionario-Datos]]).

> [!info] Pendiente de completar
> Este archivo es un placeholder. El diagrama UML y las notas de diseño sobre los gaps de integridad (§4 EntrenadorDeportista, §10 Inscripcion) se agregarán próximamente.

---

## Contenido futuro

- Diagrama de clases UML (Mermaid o imagen)
- Notas de diseño:
  - Gap en `EntrenadorDeportista`: trigger de no-solapamiento de períodos
  - Gap en `Inscripcion`: validación cruzada de `TorneoModalidad.id_torneo` ↔ `TorneoCategoria.id_torneo`

---

## Referencia

- [[DevSecOps-Proyecto/GestionDeportiva-Diccionario-Datos]]
- [[DevSecOps-Proyecto/00-Visión-General]]

---
#diagrama #uml #gestion-deportiva #placeholder