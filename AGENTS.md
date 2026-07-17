# AGENTS.md — Reglas para trabajar en este vault (ApuntesEstudio)

Este repo es una bóveda de Obsidian con apuntes de ciberseguridad y DevSecOps,
organizada en 3 carpetas principales: `Ciberseguridad/`, `DevSecOps-Estudio/`,
`DevSecOps-Proyecto/`. Sigue estas reglas SIEMPRE, en cualquier tarea que
te pida sobre este repo, sin que tenga que repetirlas.

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

## 2. Regla de links

- Todo `[[wikilink]]` que crees o edites debe apuntar a un archivo que
  realmente existe. Antes de terminar cualquier tarea que toque múltiples
  notas, verificá los links rotos con este script (o uno equivalente):

```python
import re, os
all_paths = []
for root, dirs, files in os.walk('.'):
    if '.git' in root: continue
    for f in files:
        if f.endswith('.md'):
            all_paths.append(os.path.join(root, f).replace('\\','/').lstrip('./'))

link_re = re.compile(r'\[\[([^\]|#]+)')
for root, dirs, files in os.walk('.'):
    if '.git' in root: continue
    for f in files:
        if not f.endswith('.md'): continue
        path = os.path.join(root, f)
        content = open(path, encoding='utf-8').read()
        for m in link_re.finditer(content):
            target = m.group(1).strip() + '.md'
            if not any(p == target or p.endswith('/' + target) for p in all_paths):
                print(f"ROTO: {path} -> {m.group(1)}")
```

- Nunca dejes un archivo `.md` vacío (0 bytes) en el repo — ni como
  placeholder. Si necesitás un placeholder, escribí al menos un título y
  una línea tipo `> Pendiente de completar`.

## 3. Estilo de contenido (mantené consistencia con lo existente)

- Notas de seguridad: usar tablas para comparaciones, bloques ` ``` ` para
  diagramas ASCII de flujo, callouts `> [!tip]`, `> [!warning]`,
  `> [!info]`, `> [!note]` de Obsidian donde aporten.
  Ojo con el callout: la sintaxis correcta es `> [!tipo] Título` en una
  línea y el cuerpo en líneas siguientes con `>` — nunca todo en una sola
  línea pegada (bug ya presente en `Patrones/Best-Practices.md`, línea 3).
- Cada nota termina con:
  - Una sección `## Referencia` con links a notas relacionadas
    (mínimo el índice de su carpeta).
  - Una línea de tags al final: `#tag1 #tag2 #tag3`.
- Ejemplos de código en C#/.NET: siempre mostrar `❌ MAL` vs `✅ BIEN` con
  comentario de una línea abajo explicando **por qué** es malo — es el
  formato que ya usa `Anti-Patrones-Seguridad.md`, mantenelo.
- Si agregás una tabla de mapeo a OWASP Top 10, usá **siempre la versión
  2025** (A01–A10 vigente: Broken Access Control, Security Misconfiguration,
  Software Supply Chain Failures, Cryptographic Failures, Injection,
  Insecure Design, Authentication Failures, Software or Data Integrity
  Failures, Security Logging and Alerting Failures, Mishandling of
  Exceptional Conditions). Si citás la de 2021 por contexto histórico,
  aclaralo explícitamente como "versión 2021, histórica".

## 4. Antes de terminar cualquier tarea sobre este repo

Corré ambos scripts de las secciones 1 y 2. Si encontrás algo roto que NO
te pidieron arreglar, no lo edites sin avisar — reportalo al final de tu
respuesta como "Encontré esto de paso, ¿lo corrijo también?".

#logging #owasp #config #git
