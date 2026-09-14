---
type: guía
state: activa
priority: alta
created: 2026-09-14
updated: 2026-09-14
related: [MOC - Docker, MOC - Virtualización, Pi-hole, Docker-Fundamentos]
tags: [pi-hole, docker, docker-compose, dns, instalación, homelab, privacidad]
aliases: [Instalación Pi-hole, Pi-hole Docker, Setup Pi-hole]
---

# Guía - Pi-hole con Docker

> [!info] Objetivo
> Levantar **Pi-hole** como filtro DNS dentro de un contenedor Docker en una VM Debian, y apuntar el host a esa VM como DNS. Los fundamentos teóricos están en [[40 - RECURSOS/Conceptos/Pi-hole|Pi-hole]] y [[40 - RECURSOS/Conceptos/Docker-Fundamentos|Docker — Fundamentos]].

## Prerequisitos

- **VM base:** Debian 13+ con SSH, sudo y al menos 2 GB de RAM
- **Red VM:** NAT (`192.168.122.x`)
- **Host con:** libvirt + virsh (para snapshots)

Verificar conectividad:

```bash
ssh pixel@192.168.122.x
sudo whoami   # debe devolver "root"
```

---

## Paso 1: Snapshot base

> [!warning] Antes de modificar nada
> Snapshot "limpio" por si la instalación rompe algo. Snapshot ≠ backup.

```bash
# Desde el host
virsh --connect qemu:///system snapshot-create-as debian-prueba base_limpia \
  "Debian con SSH y sudo instalados"

virsh --connect qemu:///system snapshot-list debian-prueba
```

Para revertir si falla: `virsh --connect qemu:///system snapshot-revert debian-prueba base_limpia`.

---

## Paso 2: Instalar Docker

### 2.1 Actualizar e instalar dependencias

```bash
ssh pixel@192.168.122.x
sudo apt update
sudo apt install -y ca-certificates curl
```

### 2.2 Añadir repositorio oficial (formato .asc moderno)

```bash
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg \
  -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/debian
Suites: $(. /etc/os-release && echo "$VERSION_CODENAME")
Components: stable
Architectures: $(dpkg --print-architecture)
Signed-By: /etc/apt/keyrings/docker.asc
EOF
```

### 2.3 Instalar Docker + Compose

```bash
sudo apt update
sudo apt install -y \
  docker-ce docker-ce-cli containerd.io \
  docker-buildx-plugin docker-compose-plugin
```

### 2.4 Docker sin sudo

```bash
sudo usermod -aG docker pixel
exit   # reconectar

ssh pixel@192.168.122.x
docker ps   # debe funcionar sin sudo
```

### 2.5 Verificar

```bash
docker --version
docker compose version
docker run hello-world
```

---

## Paso 3: Crear `compose.yml`

```bash
mkdir -p ~/pihole
cd ~/pihole
nano compose.yml
```

```yaml
version: '3.8'

services:
  pihole:
    container_name: pihole
    image: pihole/pihole:latest
    ports:
      - "53:53/tcp"
      - "53:53/udp"
      - "8080:80/tcp"
    environment:
      TZ: 'America/Argentina/Buenos_Aires'
      WEBPASSWORD: 'tu-contraseña-aqui'
      FTLCONF_dns_listeningMode: 'ALL'
    volumes:
      - './etc-pihole:/etc/pihole'
      - './etc-dnsmasq.d:/etc/dnsmasq.d'
    cap_add:
      - NET_ADMIN
    restart: unless-stopped
```

> [!warning] Pi-hole v6 y la contraseña
> `WEBPASSWORD` puede no funcionar en Pi-hole v6; usar `FTLCONF_webserver_api_password`. Si igual falla, reseteo en vivo: `docker exec -it pihole pihole -a -p`.

`FTLCONF_dns_listeningMode: 'ALL'` es **obligatorio** para que FTL escuche en la interfaz correcta y el port mapping funcione (ver Caso 1 en Troubleshooting).

---

## Paso 4: Levantar el servicio

```bash
cd ~/pihole
docker compose up -d

docker ps    # estado "healthy"
docker compose logs -f   # esperar "Listening on port 53"; Ctrl+C para salir
```

---

## Paso 5: Acceso al panel

```
http://192.168.122.x:8080/admin
```

- **Usuario:** `admin`
- **Contraseña:** la de `WEBPASSWORD`

Para saber la IP: `hostname -I`.

---

## Paso 6: Validar

```bash
# Desde la VM
docker exec pihole apt-get update && apt-get install -y dnsutils

# Dominio normal → IP real
docker exec pihole nslookup google.com 127.0.0.1

# Dominio bloqueado → 0.0.0.0
docker exec pihole nslookup doubleclick.net 127.0.0.1
```

✅ Esperado para `doubleclick.net`: `Address: 0.0.0.0`.

---

## Paso 7: Apuntar el DNS del host

```bash
# En tu laptop (NO en la VM)
nmcli connection show --active
nmcli device show <interfaz> | grep -i dns

sudo nmcli connection modify TU_CONEXION ipv4.dns "192.168.122.189 192.168.80.1"
sudo nmcli connection up TU_CONEXION --ask
```

Verificar:

```bash
nmcli device show <interfaz> | grep -i dns
getent ahostsv4 doubleclick.net   # 0.0.0.0 = bloqueado
getent ahostsv4 google.com        # IP real
```

Deshacer:

```bash
sudo nmcli connection modify TU_CONEXION ipv4.dns ""
sudo nmcli connection modify TU_CONEXION ipv4.ignore-auto-dns no
sudo nmcli connection up TU_CONEXION --ask
```

> [!warning] Alcance de este cambio
> Solo afecta la conexión WiFi actual del host. Otros dispositivos (teléfono, smart TV) NO verán Pi-hole todavía: requieren configurar el router o bridge networking (avanzado).

---

## Paso 8: Automatización

```bash
# VM arranca sola (desde el host)
virsh --connect qemu:///system autostart debian-prueba

# Docker arranca solo (dentro de la VM)
systemctl is-enabled docker   # debe devolver "enabled"

# Política de reinicio del contenedor
docker inspect pihole --format '{{.HostConfig.RestartPolicy.Name}}'
# → unless-stopped: reinicia si crash o si reinicia la VM; NO si lo detenés manualmente
```

---

## Snapshot final

```bash
virsh --connect qemu:///system snapshot-create-as debian-prueba pihole_ok \
  "Pi-hole DNS funcionando, host apuntado, volúmenes persistentes"

virsh --connect qemu:///system snapshot-list debian-prueba
```

Dos puntos de referencia: `base_limpia` (Debian limpio) y `pihole_ok` (pi-hole funcionando).

---

## Troubleshooting

### Caso 1: "Docker no responde DNS desde fuera"

- **Síntoma:** Pi-hole "healthy", panel `:8080` funciona, pero `dig @192.168.122.189 google.com` da timeout.
- **Causa:** Pi-hole v6 escucha DNS solo en localhost; FTL no escucha en la interfaz correcta.
- **Solución:**
  ```yaml
  environment:
    FTLCONF_dns_listeningMode: 'ALL'
  ```
  ```bash
  docker compose down && docker compose up -d
  dig @192.168.122.189 google.com +short        # → IP
  dig @192.168.122.189 doubleclick.net +short   # → 0.0.0.0
  ```

### Caso 2: "La contraseña no me deja entrar"

- **Causa:** en Pi-hole v6 la variable correcta es `FTLCONF_webserver_api_password`.
- **Solución A** (compose.yml): reemplazar `WEBPASSWORD` por `FTLCONF_webserver_api_password` y `docker compose up -d`.
- **Solución B** (reset en vivo): `docker exec -it pihole pihole -a -p` (ingresar 2 veces).

### Caso 3: "Panel HTTP no carga aunque el puerto diga abierto"

- **Diagnóstico:** `docker compose logs pihole` buscando errores (YAML, mount, permisos). Verificar estructura de directorios:
  ```bash
  mkdir -p ~/pihole/etc-pihole ~/pihole/etc-dnsmasq.d
  chmod 755 ~/pihole/etc-pihole ~/pihole/etc-dnsmasq.d
  docker compose down && docker compose up -d
  ```

### Caso 4: "DNS del host no funciona"

- Reiniciar cache: `sudo systemctl restart systemd-resolved`
- Re-conectar: `sudo nmcli connection up WIFI_NAME --ask`
- Verificar Pi-hole corriendo: `docker ps | grep pihole`

### Caso 5: "DNS del respaldo no funciona"

- Si Pi-hole está apagado, las consultas pueden colgar 30+ segundos (glibc espera timeout). Mantener `restart: unless-stopped` o apuntar el respaldo solo mientras Pi-hole esta apagado.

### Caso 6: "IPv6 escapa del bloqueo"

- glibc resuelve IPv6 primero. Workaround temporal: Firefox → `about:config` → `network.dns.disableIPv6` = true. IPv6 real en Pi-hole queda fuera de alcance.

---

## Comandos de referencia

```bash
docker ps                          # contenedores corriendo
docker compose logs -f             # logs en vivo
docker compose up -d               # levantar
docker compose down                # detener (preserva volúmenes)
docker exec -it pihole bash        # entrar al contenedor
docker exec pihole pihole -a -p    # cambiar contraseña
docker system prune                # limpiar datos no usados

virsh --connect qemu:///system snapshot-list debian-prueba
virsh --connect qemu:///system snapshot-revert debian-prueba pihole_ok
```

## Próximos pasos

1. **LAN completa:** teléfono y otros equipos usen Pi-hole (router o bridge networking)
2. **Más servicios Docker:** Jellyfin, Vaultwarden, Gitea
3. **IaC:** Terraform + cloud-init para una segunda VM automatizada
4. **Monitoreo:** Prometheus + Grafana (ver estadísticas de Pi-hole en tiempo real)

## Referencia

- [[40 - RECURSOS/MOCs/MOC - Docker]] — containers, Compose y seguridad de imágenes
- [[40 - RECURSOS/Conceptos/Pi-hole]] — teoría: DNS, filtrado y arquitectura
- [[40 - RECURSOS/Conceptos/Docker-Fundamentos]] — imágenes, volúmenes, port mapping, Compose
- [[40 - RECURSOS/MOCs/MOC - Virtualización]] — VMs y snapshots del homelab

#pi-hole #docker #docker-compose #dns #instalación #homelab