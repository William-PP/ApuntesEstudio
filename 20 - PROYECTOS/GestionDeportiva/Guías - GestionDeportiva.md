---
type: guía
state: activa
priority: alta
created: 2026-09-10
updated: 2026-09-10
related: [GestionDeportiva]
tags: [gestion-deportiva, setup, git, gitflow, arquitectura, python, fastapi]
aliases: [Guías GestionDeportiva, Setup GestionDeportiva, Git GestionDeportiva, Arquitectura GestionDeportiva]
---

# Guías — GestionDeportiva

> [!info] Manual de trabajo del repo
> Instalación, flujo de Git, convención de commits y arquitectura del backend. Fuente: README del repo oficial (`https://github.com/William-PP/GestionDeportiva.git`).

---

## 1. Puesta en marcha (setup)

**Requisito:** Python 3.13 instalado desde [python.org](https://www.python.org/downloads/) con la opción **"Add to PATH"** marcada.

```powershell
# 1. Clonar
git clone https://github.com/William-PP/GestionDeportiva.git
cd GestionDeportiva\app_GestionDeportiva

# 2. Crear venv (cada persona crea el suyo; .venv está en .gitignore)
py -m venv .venv

# 3. Activar
.\.venv\Scripts\Activate.ps1
# Si da error de policy, una vez: Set-ExecutionPolicy -Scope CurrentUser RemoteSigned

# 4. Instalar dependencias
pip install -r requirements.txt

# 5. Ejecutar
uvicorn gestiondeportiva_api.main:app --reload
```

**Endpoints tras arrancar**

| Recurso | URL |
|---------|-----|
| API | `http://localhost:8000` |
| Documentación (Swagger) | `http://localhost:8000/docs` |
| Health check | `GET /health` → `{"status":"ok"}` |

> [!tip] Detener y salir
> Detener el servidor con `Ctrl + C`; salir del venv con `deactivate`.

**Troubleshooting**

| Problema | Solución |
|----------|----------|
| `uvicorn no se reconoce` | Venv no activado o falta `pip install -r requirements.txt` |
| Puerto 8000 ocupado | `uvicorn gestiondeportiva_api.main:app --reload --port 8099` |
| Script de activación bloqueado | `Set-ExecutionPolicy -Scope CurrentUser RemoteSigned` |
| Cambios no aplican | Asegurar `--reload` al arrancar |

---

## 2. Flujo de trabajo en Git

### Ramas

| Rama | Propósito |
|------|-----------|
| `main` | Código de entregas finales (nadie toca directo) |
| `develop` | Integración del equipo |
| `feature/devN-*` | Rama temporal de cada desarrollador |

### Reglas

- Trabajar siempre en `feature/devN-*`, **nunca** directo a `develop` ni `main`.
- Todo cambio entra a `develop` mediante **Pull Request** revisada por otra persona.
- Borrar la rama feature después de hacer merge.
- Sincronizarse a diario: `git merge develop` dentro de la rama feature (resolver conflictos apenas aparezcan).

---

## 3. Convención de commits

```
<tipo>: Descripción breve

- Detalle 1
- Detalle 2
```

**Tipos permitidos:** `feat`, `fix`, `refactor`, `docs`, `chore`.

**Ejemplos reales del repo:**

```
docs: Reescritura del README con guía de instalación y workflow
feat: Setup backend Python (FastAPI + SQLModel)
```

---

## 4. Arquitectura por capas

Estructura equivalente al proyecto de referencia **SportsLeague** (misma regla de dependencias que C#):

```
app_GestionDeportiva/
├── gestiondeportiva_api/            # = SportsLeague.API (presentación)
│   ├── main.py                      # = Program.cs (entrada de la app)
│   ├── controllers/                 # = Controllers
│   ├── core/                        # config, seguridad, CORS
│   ├── dtos/                        # request / response
│   └── mappings/
├── gestiondeportiva_domain/         # = SportsLeague.Domain (negocio puro)
│   ├── entities/                   # = Entities (SQLModel)
│   ├── enums/                      # = Enums
│   ├── interfaces/                 # = Interfaces (repos y servicios)
│   ├── service/                    # = Services
│   └── helpers/
└── gestiondeportiva_data_access/    # = SportsLeague.DataAccess (persistencia)
    ├── repositories/
    ├── seeders/
    └── migrations/
```

> [!warning] Regla de dependencias
> La capa `domain` **no importa** ni a `api` ni a `data_access`. Las dependencias apuntan desde afuera hacia el dominio, nunca al revés.

### Distribución de tareas (backend)

| Dev | Módulos |
|-----|---------|
| **Dev 1** | Persona, Entrenador, Deportista, Equipo |
| **Dev 2** | Modalidad, Categoria, Torneo, TorneoModalidad, TorneoCategoria |
| **Dev 3** | Inscripcion, Resultado, NovedadMedica, RutinaTecnico, AsistenciaEntrenamiento, ReporteBienestar |

> [!note] Detalle de entidades
> El detalle completo de entidades y endpoints se planifica en `docs/Plan_Desarrollo_Backend_GestionDeportiva.md` del repo (pendiente de crear).

---

## Entregas del proyecto (ITM)

| Entrega | Fecha | Descripción | Estado |
|---------|-------|-------------|--------|
| Entrega 1 | 02/09/2026 | Análisis y Requisitos | ✅ Completada |
| Entrega 2 | 19/10/2026 | Desarrollo Backend & Arquitectura | 🔄 En curso |
| Entrega 3 | 23/11/2026 | Aplicación completa | ⏳ Pendiente |

---

## Referencia

- [[20 - PROYECTOS/GestionDeportiva/GestionDeportiva]] — epicentro del proyecto
- [[20 - PROYECTOS/GestionDeportiva/Modelo de Datos - GestionDeportiva]] — entidades que implementa este backend
- [[Guía - Comandos Git]] — referencia rápida de comandos Git
- [[Git-y-GitHub]] — conceptos base de repositorios remotos

#gestion-deportiva #setup #git #arquitectura #python #fastapi