---
type: otro
state: activa
created: 2026-08-15
updated: 2026-08-15
tags: [meta, pará, vault, documentacion]
---

# META

Mapa del vault: arquitectura híbrida PARA + MOCs.

## Arquitectura

```
00 - META        -> mapas del vault y convenciones
10 - INBOX       -> captura rápida antes de clasificar
20 - PROYECTOS   -> con fecha de inicio y fin
30 - ÁREAS       -> responsabilidades continuas
40 - RECURSOS    -> referencia por tema
   MOCs/            -> mapas de contenido (índices temáticos)
   Conceptos/       -> notas conceptuales
   Guías & Flujos/  -> guías operativas y cheatsheets
50 - ARCHIVO     -> cerrado o fuera de vigencia
```

## Reglas de oro

1. Nunca crear nombres de archivo duplicados (los wikilinks por nombre son
   ambiguos). Si un duplicado es necesario, usar SIEMPRE ruta completa.
2. Ningún archivo `.md` vacío: mínimo título + `> Pendiente de completar.`
3. Toda nota cierra con `## Referencia` (mínimo el índice de su carpeta) y
   una línea de tags.
4. Correr la validación de links rotos y duplicados antes/después de
   cualquier reestructuración.

## Referencia
- [[README]]
- [[10 - INBOX/10-INBOX]]
- [[20 - PROYECTOS/DevSecOps-Proyecto/Flujo de un proyecto]]

#meta #pará #vault #documentacion
