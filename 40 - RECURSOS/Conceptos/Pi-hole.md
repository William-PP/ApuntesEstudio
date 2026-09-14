---
type: concepto
state: activa
priority: alta
created: 2026-09-14
updated: 2026-09-14
related: [MOC - DNS, MOC - Docker, MOC - Virtualización, Guía - Pi-hole con Docker]
tags: [pi-hole, dns, dns-filtrado, docker, privacidad, red, contenedores]
aliases: [Pi-hole, DNS Filtrado, Filtro de Anuncios DNS, Pi-Hole]
---

# Pi-hole

## Definición

**Pi-hole** es un software de **filtrado de DNS a nivel de red**: se instala en tu red local y actúa como servidor DNS que bloquea dominios de publicidad, rastreadores, malware y phishing **antes** de que lleguen a tus dispositivos.

### Analogía

Imaginá el DNS como el servicio de correo de tu casa:

- **Sin Pi-hole:** el cartero trae TODA la correspondencia (publicidad incluida).
- **Con Pi-hole:** el cartero revisa una lista de "remitentes bloqueados" y tira la publicidad ANTES de dejarla en la puerta.

El resultado: menos anuncios, menos rastreadores, más privacidad y menos datos consumidos.

### ¿Qué bloquea?

Pi-hole mantiene **listas negras de dominios** (blacklists):

- Dominios de publicidad (doubleclick.net, google-analytics.com, etc.)
- Dominios de rastreadores
- Dominios maliciosos
- Dominios de scam/phishing

Cuando un dispositivo intenta conectarse a un dominio bloqueado, Pi-hole responde `0.0.0.0` (IP nula): el navegador no obtiene respuesta y el anuncio nunca carga.

### Beneficios

| Beneficio | Por qué |
|-----------|---------|
| **Sin anuncios** | Los anuncios nunca llegan a tu navegador |
| **Más privacidad** | Los rastreadores no pueden seguirte |
| **Menos datos** | No descargas imágenes de anuncios |
| **Más rápido** | Saltas todo el código de tracking |
| **A nivel de red** | Protege TODOS los dispositivos (teléfono, smart TV, laptop) |

## Por Qué Importa

Es la pieza central de un homelab orientado a privacidad: en lugar de depender del DNS de tu ISP (que registra "qué sitios visitás"), **vos** controlás las respuestas DNS de toda tu red. Es también una capa de defensa contra malware basado en dominio.

## DNS (Contexto necesario)

DNS (Domain Name System) traduce nombres legibles a IPs:

```
Tú escribes:  google.com
     ↓
DNS responde: 142.251.32.46
     ↓
Tu navegador conecta a 142.251.32.46
```

Por defecto, tu operadora (o Google/Cloudflare) responde las consultas y **sabe exactamente a qué sitios visitás**. Con Pi-hole, **todas las consultas DNS de tu red pasan por él primero**:

```
Tu dispositivo:  ¿Dónde está doubleclick.net?
     ↓
Pi-hole (local): dominios bloqueado → 0.0.0.0
     ↓
Tu dispositivo:  bloqueado, sin anuncio
```

### Flujo de una consulta

```
1. Laptop: dig google.com @192.168.122.189
                ↓
2. Pi-hole recibe consulta en puerto 53 (DNS)
                ↓
3. Revisa listas negras
                ↓
4. ¿Está en lista negra?
         ├─ SÍ: Responde 0.0.0.0 (bloqueado)
         └─ NO: Pregunta a upstream (ISP/Cloudflare)
                    ↓
                    Responde IP real
                ↓
5. Laptop recibe respuesta
```

## Arquitectura

```
┌─────────────────────────────────────────┐
│ Dispositivo (Laptop, Teléfono, etc.)    │
│   DNS: 192.168.122.189 (VM con Pi-hole) │
├─────────────────────────────────────────┤
│ Pi-hole (en contenedor Docker)          │
│  ├─ FTL (motor DNS)                     │
│  ├─ Listas negras (blacklists)          │
│  ├─ Listas blancas (whitelist)          │
│  └─ API REST                            │
├─────────────────────────────────────────┤
│ Panel web (puerto 8080)                 │
│  ├─ Estadísticas                        │
│  ├─ Listas de bloqueo                   │
│  ├─ Whitelist/Blacklist                 │
│  └─ Configuración                       │
├─────────────────────────────────────────┤
│ Upstream DNS (ISP, Cloudflare, etc.)    │
│  └─ Solo si el dominio NO está bloqueado│
└─────────────────────────────────────────┘
```

### Variables de entorno clave

```yaml
environment:
  TZ: 'America/Argentina/Buenos_Aires'   # Zona horaria
  WEBPASSWORD: 'contraseña'              # Contraseña del panel
  FTLCONF_dns_listeningMode: 'ALL'       # Escucha en todas las interfaces
```

Estas variables configuran Pi-hole **dentro del contenedor** sin tocar archivos de configuración.

> [!warning] Pi-hole v6: contraseña
> En Pi-hole v6 la variable `WEBPASSWORD` puede no funcionar; la correcta es `FTLCONF_webserver_api_password`. Si no entra, resetear en vivo con `docker exec -it pihole pihole -a -p`(revisar documentacion oficial).

## Relación con el Stack del Homelab

```
VM (Debian)
├─ Docker Engine (gestor de contenedores)
├─ Docker Daemon (ejecuta contenedores)
└─ Contenedor pihole (Pi-hole DNS running)
    ├─ Puerto 53 (DNS)
    ├─ Puerto 8080 (Web)
    └─ Volúmenes (config)

Red:
├─ VM: 192.168.122.189 (en red NAT)
├─ Host: puede conectar por SSH
└─ Dispositivos en LAN: pueden usar Pi-hole como DNS
```

Pi-hole vive dentro de un contenedor Docker; los conceptos que lo sostienen (imagen vs contenedor, Compose, volúmenes, port mapping) están detallados en [[40 - RECURSOS/Conceptos/Docker-Fundamentos|Docker — Fundamentos]].

> [!tip] Snapshot antes de instalar
> Antes de instalar Docker + Pi-hole, creá un snapshot de la VM limpia: si algo se rompe, revertís el estado en segundos. Snapshot ≠ backup (ver [[40 - RECURSOS/Conceptos/Virtualización|Virtualización]]).

## Referencia

- [[40 - RECURSOS/MOCs/MOC - DNS]] — DNS desde la perspectiva de seguridad
- [[40 - RECURSOS/Guías & Flujos/Guía - Pi-hole con Docker]] — instalación paso a paso
- [[40 - RECURSOS/Conceptos/Docker-Fundamentos]] — imágenes, contenedores, Compose, volúmenes
- [[40 - RECURSOS/Conceptos/Virtualización]] — VMs y snapshots del homelab

#pi-hole #dns #dns-filtrado #docker #privacidad #red