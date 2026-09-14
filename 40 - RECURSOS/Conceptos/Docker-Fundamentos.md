---
type: concepto
state: activa
created: 2026-08-15
updated: 2026-09-13
tags: [devsecops, containers, estudio, docker, git, logging, microservices, testing, fundamentos, deploy]
aliases: [Docker, Contenedores, Containerización]
---

# Docker — Fundamentos

## QUÉ es

Docker es una plataforma de **containerización** que empaqueta una aplicación con todas sus dependencias (librerías, binarios, configuración) en un **contenedor** que se ejecuta igual en cualquier lugar.

**El problema que resuelve:**

| Sin Docker | Con Docker |
|-----------|-----------|
| "En mi máquina funciona" | Funciona en todas partes |
| Instalar dependencias manualmente | Todo empaquetado en la imagen |
| Conflictos de versiones | Aislamiento perfecto |
| Deploy manual | Deploy consistente |

| Concepto | Definición | Analogía |
|----------|-----------|----------|
| **Dockerfile** | Receta para construir una imagen | Recipe de cocina |
| **Imagen** | Template inmutable (archivo comprimido) | El molde de pastel |
| **Container** | Instancia corriendo de una imagen | El pastel horneado |
| **Registry** | Repositorio de imágenes (Docker Hub) | App Store de containers |
| **Volume** | Persistencia de datos del host | El refrigerador |

## CÓMO funciona

```
Dockerfile → docker build → Imagen → docker run → Container
                                                    (proceso corriendo)
```

### Arquitectura

```
┌─────────────────────────────────┐
│   Docker CLI (cliente)          │  ← tus comandos (docker run, ps…)
├─────────────────────────────────┤
│   Docker Daemon (servidor)      │  ← dockerd, ejecuta realmente
├─────────────────────────────────┤
│   containerd                    │  ← gestor de contenedores de bajo nivel
├─────────────────────────────────┤
│   runc                          │  ← crea contenedores (namespaces + cgroups)
├─────────────────────────────────┤
│   Kernel Linux + Hardware       │
└─────────────────────────────────┘
```

Cuando ejecutas `docker run ubuntu echo "Hola"`: la **CLI** envía la petición al **Daemon**, que prepara todo; **containerd** gestiona el contenedor y **runc** lo crea usando namespaces/cgroups del kernel. Todo en milisegundos.

### Imágenes = capas (layers)

```
┌─────────────────────────────┐
│ Capa 4: Tu app              │ ← 10 MB
├─────────────────────────────┤
│ Capa 3: Dependencias        │ ← 50 MB
├─────────────────────────────┤
│ Capa 2: Node.js / runtime   │ ← 100 MB
├─────────────────────────────┤
│ Capa 1: Base (Debian/slim)  │ ← 70 MB
└─────────────────────────────┘
```

Las capas permiten **reutilización** (dos imágenes comparten la misma base sin descargar dos veces), **caché** (si cambias tu código solo se reconstruye la última capa) y **eficiencia**.

### Ciclo de vida de un contenedor

```
docker run → CREADO → RUNNING → (docker stop) → STOPPED → (docker rm) → ELIMINADO
```

| Estado | Significa | Recuperable |
|--------|-----------|------------|
| **Created** | Creado pero no inicia | Sí |
| **Running** | Ejecutándose | Sí |
| **Stopped/Exited** | Parado pero preservado | Sí |
| **Dead** | Error | No |
| **Removed** | Eliminado | No |

## VMs vs Contenedores

```
VM:                     Contenedor:
┌────────────────┐      ┌────────────────┐
│ Aplicación     │      │ Aplicación     │
│ SO Guest (GBs) │      │ Dependencias   │
│ Kernel         │      │ Binarios       │  ← imagen (100–500 MB)
├────────────────┤      │ Filesystem     │
│ Hipervisor     │      ├────────────────┤
│ SO Host        │      │ Docker Engine  │
│ Hardware       │      │ SO Host        │  ← comparte el kernel
└────────────────┘      │ Hardware       │
                        └────────────────┘
```

| Aspecto | Máquina Virtual | Contenedor Docker |
|--------|-----------------|-------------------|
| **Tamaño** | 1–10 GB | 50–500 MB |
| **Tiempo boot** | 30–60 s | 100–500 ms |
| **Cantidad por host** | 5–10 | 100+ |
| **Aislamiento** | Total | Nivel proceso |
| **Overhead de recursos** | Alto | Bajo |
| **Caso de uso** | Múltiples SOs distintos | Microservicios |

> [!note] ¿Cuándo cada uno?
> **VM**: necesitas Windows y Linux juntos, testing en arquitecturas distintas, o aislamiento fuerte (ciberseguridad). **Contenedores**: múltiples apps independientes, microservicios, escalabilidad horizontal.

## CUÁNDO usar Docker

| Caso | ¿Docker? |
|------|---------|
| Desarrollo local | ✅ Sí |
| Testing / CI/CD | ✅ Sí |
| Producción | ✅ Sí |
| Microservicios | ✅ Sí |
| Monolito pequeño | ⚠️ Opcional |

### Ventajas y desventajas

| ✅ Ventajas | ❌ Desventajas |
|-------------|---------------|
| Consistencia (misma imagen en todos lados) | Curva de aprendizaje |
| Ligero (MB vs GB de VMs) | Storage: muchas imágenes pesan |
| Arranque en ms | Debugging más difícil |
| Escalabilidad horizontal | Networking complejo |
| Aislamiento entre contenedores | Datos no persisten sin volumes |
| Versionado de imágenes (v1.0, v1.1…) | Menos aislamiento que VMs (seguridad) |
| Integración perfecta con pipelines | Bases de datos (stateful) difíciles |

## Dockerfile

### Instrucciones principales

| Instrucción | Para qué |
|------------|---------|
| `FROM` | Imagen base (DEBE ser la primera) |
| `RUN` | Ejecuta comandos durante la construcción |
| `COPY` | Copia archivos del host al contenedor |
| `ADD` | Como COPY pero auto-descomprime tar |
| `WORKDIR` | Directorio de trabajo (como cd) |
| `ENV` | Variables de entorno |
| `EXPOSE` | Documenta el puerto (no lo publica: se necesita `-p`) |
| `CMD` | Comando por defecto (sobrescribible) |
| `ENTRYPOINT` | Ejecutable principal (no se sobrescribe fácil) |
| `VOLUME` | Puntos de montaje |

> [!warning] Optimización de capas
> Cada `RUN` crea una capa → más capas = imagen más grande.
> ```dockerfile
> # ❌ MAL: 3 capas innecesarias y basura de apt incluida
> RUN apt-get update
> RUN apt-get install -y curl
> RUN rm -rf /var/lib/apt/lists/*
>
> # ✅ BIEN: un solo RUN encadenado con cleanup
> RUN apt-get update && apt-get install -y curl && rm -rf /var/lib/apt/lists/*
> ```

### Multi-stage Build (imagen pequeña)

```dockerfile
# Stage 1: Compilar
FROM mcr.microsoft.com/dotnet/sdk:10.0 AS build
WORKDIR /src
COPY . .
RUN dotnet publish -c Release -o /app

# Stage 2: Ejecutar (imagen pequeña)
FROM mcr.microsoft.com/dotnet/aspnet:10.0
WORKDIR /app
COPY --from=build /app .
ENTRYPOINT ["dotnet", "MyApp.dll"]
```

**Resultado:** imagen de ~110MB en vez de ~800MB.

### Ejemplo práctico (Flask)

```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
EXPOSE 5000
CMD ["python", "app.py"]
```

```bash
docker build -t mi-app:1.0 .
docker run -p 5000:5000 mi-app:1.0   # → http://localhost:5000
```

## Volumes (persistencia)

**Problema:** `docker rm contenedor` borra los datos del contenedor.

```bash
# Volumen nombrado (gestionado por Docker)
docker volume create my-data
docker run -v my-data:/app/data ubuntu bash

# Bind mount (directorio específico del host)
docker run -v /home/wilk/datos:/app/data ubuntu bash

# tmpfs (solo RAM, se borra al apagar)
docker run --tmpfs /app/cache ubuntu bash
```

Casos típicos: `-v db-data:/var/lib/postgresql` (BD), `-v $PWD:/app` (desarrollo), `-v /var/log/app:/app/logs` (logs).

## Redes

Por defecto los contenedores están aislados. Para conectarlos:

```bash
docker network create my-app
docker run -d --network my-app --name db postgres
docker run -d --network my-app --name api node app.js
# Dentro de "api": curl http://db:5432  ← funciona por DNS interno
```

| Red | Uso |
|-----|-----|
| **Bridge** (default) | Aislamiento + NAT contra el host |
| **Host** | Comparte red del host (performance, menos aislamiento) |
| **Overlay** | Multi-host (Docker Swarm) |
| **Macvlan** | El contenedor tiene IP propia en la red física |

## Docker Compose

Define aplicaciones multi-contenedor en YAML:

```yaml
services:
  db:
    image: postgres:15
    environment:
      POSTGRES_PASSWORD: secret
    volumes:
      - db-data:/var/lib/postgresql/data
  api:
    build: .
    ports: ["5000:5000"]
    depends_on: [db]
    environment:
      DATABASE_URL: postgresql://postgres:secret@db:5432/myapp

volumes:
  db-data:
```

```bash
docker compose up -d     # levantar en background
docker compose logs -f   # logs en vivo
docker compose down      # detener
docker compose down -v   # detener + borrar volúmenes
```

## Instalación (Linux/Debian)

**Paquetes previos:** `apt-transport-https ca-certificates curl gnupg lsb-release software-properties-common`.

```bash
# Repositorio oficial + clave GPG
sudo curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt update

# Instalar Docker CE
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin

# Iniciar y habilitar
sudo systemctl enable --now docker
sudo docker run hello-world   # verificación
sudo usermod -aG docker $USER # permisos sin sudo (recargar sesión: newgrp docker)
```

> [!warning] Paquetes que NO necesitas
> `docker.io` (viejo), `docker-compose` v1 (hoy es plugin integrado: `docker compose`), `lxc` (Docker usa containerd), `aufs-tools` (usa overlay2 del kernel).

## Comandos esenciales

> [!note] Referencia de comandos
> La cheatsheet completa (imágenes, containers, debugging, compose, seguridad) está en la [[40 - RECURSOS/Guías & Flujos/Guía - Comandos Docker|Guía - Comandos Docker]]. Flujo básico: `docker build` → `docker run` → `docker ps` → `docker logs`.

```bash
docker pull ubuntu:22.04       # descargar imagen
docker images                  # listar imágenes
docker run -it ubuntu bash     # contenedor interactivo
docker run -d --name web -p 8080:80 nginx   # detach + puerto
docker ps -a                   # listar contenedores
docker exec -it web bash       # bash dentro de contenedor corriendo
docker logs -f web             # logs en vivo
docker stop/kill/rm web        # detener / matar / eliminar
docker system prune -a         # limpiar todo lo no usado
```

## Troubleshooting rápido

| Error | Solución |
|-------|----------|
| "Cannot connect to the Docker daemon" | `sudo systemctl start docker` |
| "Permission denied while trying to connect" | `sudo usermod -aG docker $USER` + `newgrp docker` |
| Contenedor se detiene de inmediato | `docker logs <id>` y correr `-it imagen bash` |
| "No space left on device" | `docker system df` + `docker system prune` |

## Próximos pasos

```
Semana 1: Ejecutar contenedores (docker run)
↓
Semana 2: Crear imágenes (Dockerfile)
↓
Semana 3: Multi-contenedor (Docker Compose)
↓
Semana 4+: Orquestación (Kubernetes)
```

Usa Docker en tu homelab junto con las VMs de virtualización; como complemento de seguridad universal: escanea las imágenes con Trivy y nunca corras procesos como root.

## Referencia

- [[40 - RECURSOS/MOCs/MOC - Docker]] — mapa completo del dominio
- [[40 - RECURSOS/Guías & Flujos/Guía - Comandos Docker]] — cheatsheet de comandos
- [[40 - RECURSOS/Guías & Flujos/Guía - Dockerfile Seguro]] — Dockerfile seguro (non-root, .dockerignore)
- [[40 - RECURSOS/Guías & Flujos/Guía - Docker Compose]] — orquestar app + BD localmente
- [[40 - RECURSOS/Guías & Flujos/Guía - Trivy]] — escaneo de vulnerabilidades
- [[40 - RECURSOS/MOCs/MOC - Virtualización]] — VMs como alternativa/complemento
- [[40 - RECURSOS/MOCs/MOC - Kubernetes]] — orquestación a escala

#devsecops #containers #estudio #docker #git #logging #microservices #testing #fundamentos #deploy