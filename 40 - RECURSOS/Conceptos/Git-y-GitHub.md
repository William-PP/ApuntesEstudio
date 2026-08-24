---
type: concepto
state: activa
created: 2026-08-23
updated: 2026-08-23
tags: [git, github, devops, control-de-versiones, branching, colaboracion]
aliases: [Git, GitHub]
---

# Git y GitHub — Conceptos Fundamentales

## 1. Git: Control de Versiones Local

**Git es un sistema de control de versiones distribuido.** Guarda snapshots (fotos) de tu código en momentos específicos, permite volver atrás, ver historial y comparar versiones.

### El problema que resuelve

Sin Git:
```
proyecto_v1.py
proyecto_v2.py
proyecto_viejo.py
proyecto_FINAL.py
proyecto_FINAL_FINAL.py
```

Con Git:
```
Commit 1: "Crear estructura base"
Commit 2: "Agregar funciones de usuario"
Commit 3: "Arreglar bug en autenticación"
```

### Comandos esenciales

| Comando | Qué hace |
|---------|----------|
| `git init` | Crea un repositorio nuevo |
| `git add .` | Prepara todos los cambios para commitear |
| `git commit -m "mensaje"` | Guarda un snapshot con mensaje descriptivo |
| `git log` | Muestra historial de commits |
| `git diff` | Compara cambios entre versiones |
| `git blame archivo` | Muestra quién editó cada línea |

### Cadena de commits

```
Commit 1 (Inicial)
    |
Commit 2 (Feature A)
    |
Commit 3 (Bug fix)
    |
Commit 4 (Feature B)  <-- HEAD (estás aquí)
```

---

## 2. GitHub: Colaboración en la Nube

**GitHub es una plataforma web** que almacena repositorios Git en sus servidores para colaboración, backup e historial público.

### Git vs GitHub

| Característica | Git | GitHub |
|----------------|-----|--------|
| Ubicación | Local (tu máquina) | Nube (internet) |
| Funciona sin internet | Si | No |
| Colaboración en equipo | Limitada | Completa |
| Backup automático | No | Si |
| Interfaz visual | No | Si |

### Flujo básico

```
Tu máquina  --> git commit --> Git local
                  |
            git push
                  v
            GitHub (remoto)
                  |
            git pull
                  v
         Compañero (su máquina)
```

---

## 3. Repositorio: Estructura de un Proyecto

Un **repositorio** es una carpeta con una subcarpeta oculta `.git` que contiene todo el historial.

```
mi-proyecto/
├── .git/              <-- Carpeta mágica (no editar)
│   ├── objects/       <-- Snapshots comprimidos
│   ├── refs/          <-- Referencias a ramas
│   └── HEAD           <-- Posición actual
├── src/
├── docs/
└── README.md
```

### Tipos de repositorio

| Tipo | Ubicación | Uso |
|------|-----------|-----|
| **Local** | `~/proyecto/.git` | Trabajo diario |
| **Remoto** | `github.com/usuario/proyecto` | Backup, equipo |

---

## 4. Commits: Snapshots de Código

Un **commit** es un snapshot con metadatos:

```
Hash:     a1b2c3d
Autor:    William PP
Fecha:    2026-08-22 14:30
Mensaje:  "GD-5: Agregar narrativa de la aplicacion"
```

### Por qué importan

| Beneficio | Ejemplo |
|-----------|---------|
| Historial completo | `git log` muestra todos los cambios |
| Volver atras | `git revert a1b2c3d` deshace un commit |
| Busqueda | `git log --grep="email"` encuentra menciones |
| Auditoria | `git blame` dice quien cambio cada linea |

---

## 5. Ramas: Universos Paralelos

Las **ramas** permiten trabajar en funcionalidades sin afectar el codigo principal.

### Sin ramas (caos)

```
Todos editan main  ->  Conflictos constantes
```

### Con ramas (profesional)

```
main (codigo estable)
  |-- feature/GD-5 (Persona 1)
  |-- feature/GD-6 (Persona 2)
  |-- feature/GD-8 (Persona 3)
```

### Comandos de ramas

```bash
git branch feature/nueva-funcion    # Crear rama
git checkout feature/nueva-funcion  # Cambiarte a ella
git checkout -b feature/nueva-funcion  # Crear + cambiar
git merge feature/nueva-funcion     # Fusionar a la rama actual
git branch -d feature/nueva-funcion # Borrar rama
```

### Ejemplo: Experimentar sin miedo

```bash
git checkout -b experiment/idea-loca
# Trabajas sin tocar main

# Si funciona:
git checkout main
git merge experiment/idea-loca

# Si no funciona:
git branch -d experiment/idea-loca  # Se borra sin problemas
```

---

## 6. Estrategia de Ramas: Git Flow Simplificado

### Estructura recomendada para equipos

```
MAIN (produccion, nunca se toca directamente)
  ^
  | (merge cuando hay entrega)
  |
DEVELOP (integracion del equipo)
  ^
  | (PR desde features)
  |
FEATURES (trabajo individual)
  feature/GD-5-narrativa
  feature/GD-6-story-mapping
  feature/GD-8-diagrama
```

### Ciclo completo

```
Lunes:    Cada persona crea su feature branch desde develop
Viernes:  Hacen Pull Request a develop
          Companeros revisan y aprueban
          Merge a develop
Entrega:  merge develop -> main -> push
```

### Reglas de oro

| Regla | Detalle |
|-------|---------|
| Nunca commitear a main directamente | Siempre usar feature branches |
| Siempre crear PR antes de merge | Revision obligatoria |
| Pull cada manana | Sincronizarse con el equipo |
| Comunicar cambios importantes | Avisar antes de refactorizar |

---

## 7. Pull Requests: Revision de Codigo

Un **PR** es una solicitud formal: "Revisen mi trabajo antes de fusionarlo".

### Flujo del PR

```
1. Creas PR con titulo y descripcion
2. Companeros revisan cambios
3. Dan feedback o aprueban
4. Tu arreglas si es necesario
5. Merge cuando esta listo
```

### Estructura de un buen PR

```markdown
# Titulo: GD-5: Modelo Verbal (Narrativa)

## Cambios
- Definir actores principales
- Describir flujos de negocio
- Documentar casos de excepcion

## Archivos modificados
- docs/01_Narrativa.md (nuevo)

## Checklist
- [x] Revisado
- [x] Sin conflictos con develop
- [x] Listo para merge
```

---

## 8. Conflictos: Cuando Dos Personas Editan lo Mismo

### Cuándo ocurren

**Conflicto = dos personas editan la misma linea del mismo archivo.**

```
Persona 1 edita linea 5:  "Actores: Admin, Deportista, Arbitro"
Persona 2 edita linea 5:  "Actores principales: Admin y User"
Git no sabe cual es correcta -> CONFLICTO
```

### Como prevenirlos

| Regla | Accion |
|-------|--------|
| Pull cada manana | `git pull origin develop` antes de trabajar |
| Archivos diferentes | Cada persona edita archivos distintos |
| Comunicacion | Avisar antes de refactorizar |

### Como resolverlos

**Paso 1:** Ver el conflicto en el archivo:
```markdown
<<<<<<< HEAD
Los actores son Admin, Deportista y Arbitro
=======
Los actores principales: Admin y User
>>>>>>> develop
```

**Paso 2:** Elegir una opcion:
1. Quedarse con tu version
2. Quedarse con la otra version
3. Combinar ambas manualmente

**Paso 3:** Guardar y committear:
```bash
git add .
git commit -m "Resolver conflicto en narrativa"
git push
```

---

## 9. Beneficios de Usar Git y GitHub

| Beneficio | Descripcion |
|-----------|-------------|
| Historial | `git log` muestra todo lo que paso |
| Seguridad | `git revert` revierte cambios sin perder nada |
| Colaboracion | 3 personas trabajan sin pisarse |
| Documentacion | Historial de commits = documentacion automatica |
| Auditoria | `git blame` dice quien hizo que y cuando |

---

## Referencia
- [[40 - RECURSOS/MOCs/MOC - DevSecOps]]
- [[40 - RECURSOS/Guías & Flujos/Guía - Comandos Git]]
- [[40 - RECURSOS/MOCs/MOC - CI-CD]]

#git #github #devops #control-de-versiones #branching #colaboracion
