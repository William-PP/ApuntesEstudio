# AGENTS.md — Reglas para trabajar en este vault (ApuntesEstudio)

Este repo es una bóveda de Obsidian con apuntes de ciberseguridad y DevSecOps,
organizada con una arquitectura híbrida **PARA + MOCs**:

```
00 - META            mapas del vault y convenciones
10 - INBOX           captura rápida antes de clasificar
20 - PROYECTOS       con fecha de inicio y fin (ej. DevSecOps-Proyecto, Minipensum)
30 - ÁREAS           responsabilidades continuas
40 - RECURSOS        referencia por tema
   MOCs/                mapas de contenido (índices temáticos por tema)
   Conceptos/           notas conceptuales
   Guías & Flujos/      guías operativas y cheatsheets
50 - ARCHIVO         cerrado o fuera de vigencia
```

Sigue estas reglas SIEMPRE, en cualquier tarea que te pida sobre este repo,
sin que tenga que repetirlas.

> [!warning] Documento maestro obligatorio
> **`00 - META/PROTOCOLO-PKM-VAULT.md`** es el protocolo de referencia para
> CUALQUIER operación en el vault: frontmatter YAML obligatorio, nomenclatura
> (`MOC - [Tema]`, `Guía - [Acción]`, `[PROYECTO] - [Nombre]`, `[ÁREA] - [Nombre]`),
> formatos (❌ MAL / ✅ BIEN, callouts, cierre con `## Referencia` + tags) y
> protocolos de mantenimiento. Leerlo SIEMPRE antes de crear, modificar o
> sugerir cambios de notas.

---

## 1. Regla de oro: nunca crear nombres de archivo duplicados

Antes de crear cualquier archivo `.md`, corré:

```bash
find . -name "*.md" -exec basename {} \; | sort | uniq -d
```

Si el nombre que vas a usar ya existe en otra carpeta, **no lo crees con ese
nombre**. Obsidian resuelve `[[wikilinks]]` sin ruta por nombre de archivo
únicamente — si hay dos archivos `Foo.md` en carpetas distintas, cualquier
link `[[Foo]]` sin ruta completa es ambiguo y puede resolver al archivo
equivocado. Este vault ya tuvo bugs reales por esto (ver sección 4).

Si dos archivos "deberían" llamarse igual porque cubren el mismo tema en
capas distintas (ej. una nota conceptual y una nota aplicada a un proyecto),
resolvé el conflicto de una de estas formas, en este orden de preferencia:
1. Renombrar uno para que sea descriptivo de su capa (ej. `Health-Checks.md`
   → `Health-Checks-Proyecto.md` si es la versión aplicada).
2. Si mantenés el nombre duplicado a propósito, todo link hacia cualquiera
   de los dos SIEMPRE debe usar ruta completa: `[[Carpeta/Subcarpeta/Nombre]]`,
   nunca `[[Nombre]]` a secas.

## 2. Ubicación de notas nuevas (regla de colocación)

Al crear o mover una nota, ubicarla según su naturaleza:
- **Índice temático / hub** → `40 - RECURSOS/MOCs/`.
- **Nota conceptual** (teoría, explicación de un tema) → `40 - RECURSOS/Conceptos/`.
- **Guía operativa / cheatsheet / flujo de trabajo** → `40 - RECURSOS/Guías & Flujos/`.
- **Trabajo con fecha de inicio y fin** → `20 - PROYECTOS/` (cada proyecto en su subcarpeta).
- **Responsabilidad continua** → `30 - ÁREAS/`.
- **Captura sin clasificar** → `10 - INBOX/` (y se clasifica en la siguiente revisión).
- **Obsoleto / cerrado** → `50 - ARCHIVO/`.
- **Convenciones y mapas del vault** → `00 - META/`.

## 3. Regla de links

- Todo `[[wikilink]]` que crees o edites debe apuntar a un archivo que
  realmente existe (la resolución es **insensible a mayúsculas**, igual que
  Obsidian). Antes de terminar cualquier tarea que toque múltiples notas,
  verificá los links rotos con este script (o uno equivalente):

```python
import re, os
all_paths = []
for root, dirs, files in os.walk('.'):
    if '.git' in root: continue
    for f in files:
        if f.endswith('.md'):
            all_paths.append(os.path.join(root, f).replace('\\','/').lstrip('./'))
all_paths_l = [p.lower() for p in all_paths]

link_re = re.compile(r'\[\[([^\]|#]+)')
for root, dirs, files in os.walk('.'):
    if '.git' in root: continue
    for f in files:
        if not f.endswith('.md'): continue
        if f == 'AGENTS.md' or f == 'PROTOCOLO-PKM-VAULT.md': continue
        path = os.path.join(root, f)
        content = open(path, encoding='utf-8').read()
        for m in link_re.finditer(content):
            target = (m.group(1).strip() + '.md').lower()
            if not any(p.lower() == target or p.lower().endswith('/' + target) for p in all_paths):
                print(f"ROTO: {path} -> {m.group(1)}")
```

> [!note] Nota sobre el checker
> `PROTOCOLO-PKM-VAULT.md` queda excluido del chequeo: contiene wikilinks de
> ejemplo dentro de bloques de código, no enlaces reales.

- Nunca dejes un archivo `.md` vacío (0 bytes) en el repo — ni como
  placeholder. Si necesitás un placeholder, escribí al menos un título y
  una línea tipo `> Pendiente de completar.`

## 4. Estilo de contenido (mantené consistencia con lo existente)

- Notas de seguridad: usar tablas para comparaciones, bloques ` ``` ` para
  diagramas ASCII de flujo, callouts `> [!tip]`, `> [!warning]`,
  `> [!info]`, `> [!note]` de Obsidian donde aporten.
  Ojo con el callout: la sintaxis correcta es `> [!tipo] Título` en una
  línea y el cuerpo en líneas siguientes con `>` — nunca todo en una sola
  línea pegada (bug ya corregido en `40 - RECURSOS/Conceptos/Best-Practices.md`).
- Cada nota termina con:
  - Una sección `## Referencia` con links a notas relacionadas
    (mínimo el índice de su carpeta o su MOC).
  - Una línea de tags al final: `#tag1 #tag2 #tag3`.
- Ejemplos de código en C#/.NET: siempre mostrar `❌ MAL` vs `✅ BIEN` con
  comentario de una línea abajo explicando **por qué** es malo — es el
  formato que ya usa `40 - RECURSOS/Conceptos/Anti-Patrones-Seguridad.md`, mantenelo.
- Si agregás una tabla de mapeo a OWASP Top 10, usá **siempre la versión
  2025** (A01–A10 vigente: Broken Access Control, Security Misconfiguration,
  Software Supply Chain Failures, Cryptographic Failures, Injection,
  Insecure Design, Authentication Failures, Software or Data Integrity
  Failures, Security Logging and Alerting Failures, Mishandling of
  Exceptional Conditions). Si citás la de 2021 por contexto histórico,
  aclaralo explícitamente como "versión 2021, histórica".

## 5. Protocolo de reporte

Cuando termines una tarea:
1. Corré el script de links rotos de la sección 3 y la comprobación de
   duplicados de la sección 1 (PowerShell: `Group-Object Name` sobre
   `Get-ChildItem -Recurse -Filter *.md`, descartando `.git`).
2. Si encontrás algo roto que NO te pidieron arreglar, no lo edites sin
   avisar — reportalo al final de tu respuesta como
   "Encontré esto de paso, ¿lo corrijo también?".

#logging #owasp #config #git
