---
type: guía
state: activa
priority: alta
created: 2026-09-13
updated: 2026-09-13
related: [MOC - Virtualización, Virtualización]
tags: [virtualización, instalación, kvm, qemu, libvirt, vm, linux, debian]
aliases: [Guía Instalación Virtualización, Setup Virtualización Linux, Crear primera VM]
---

# Guía - Instalación Virtualización

> [!info] Guía completa KVM/QEMU en Debian (homelab)
> Instalación y verificación del stack de virtualización KVM/QEMU + libvirt en Linux (Debian/Ubuntu), desde la verificación de la CPU hasta la primera VM con SO instalado. El enfoque de este manual es **Debian**, estándar de producción en Linux.

## Prerequisitos

- Linux (Debian/Ubuntu) con acceso `sudo`
- CPU con soporte de virtualización (Intel VT-x o AMD-V) **habilitado en BIOS**
- Conexión a internet para `apt` y descarga de ISOs

---

## Paso 0: Verificar soporte de virtualización del CPU

```bash
grep -E 'vmx|svm' /proc/cpuinfo | head -1
```

- `vmx` = Intel VT-x · `svm` = AMD-V

> [!warning] Si aparece vacío
> 1. Reiniciar y entrar a BIOS (Delete / F2 / F10 / F12 según fabricante)
> 2. Buscar "Intel Virtualization Technology", "Intel VT-x", "Secure Virtual Machine"/"AMD-V"
> 3. Cambiar a **Enabled**, guardar y reiniciar
> 4. Re-verificar con `grep vmx/svm`

---

## Paso 1: Instalar paquetes

```bash
sudo apt update && sudo apt install -y \
  qemu-system-x86 \
  libvirt-daemon-system \
  libvirt-clients \
  virtinst \
  virt-manager \
  virt-viewer
```

| Paquete | Instala | Capa |
|---------|---------|------|
| `qemu-system-x86` | `/usr/bin/qemu-system-x86_64` | Motor (QEMU) |
| `libvirt-daemon-system` | `/usr/sbin/libvirtd` | Gestor (libvirt) |
| `libvirt-clients` | `/usr/bin/virsh` | CLI |
| `virtinst` | `/usr/bin/virt-install` | CLI (creación de VMs) |
| `virt-manager` | `/usr/bin/virt-manager` | GUI |
| `virt-viewer` | `/usr/bin/virt-viewer` | Consola gráfica dedicada |

> [!note] Tiempo estimado
> 5–15 minutos, según la conexión.

---

## Paso 2: Configurar permisos

```bash
sudo usermod -aG kvm,libvirt $USER
```

| Comando | Efecto |
|---------|--------|
| `$USER` | Tu usuario (ej: `wilk`) |
| `kvm` | Grupo para acceso a KVM |
| `libvirt` | Grupo para acceso a libvirt |
| `-aG` | Añade a los grupos sin remover de otros |

**Activar los cambios:** cerrar sesión completamente (`gnome-session-quit`), reiniciar (`sudo reboot`) o recargar grupos (`newgrp libvirt`).

**Verificar:**
```bash
groups              # Debe incluir: libvirt kvm
virsh list --all    # Sin errores (vacío = normal la primera vez)
```

---

## Paso 3: Habilitar e iniciar libvirtd

```bash
sudo systemctl enable libvirtd   # arranque automático
sudo systemctl start libvirtd    # iniciar ahora
```

> [!note] qemu:///system vs qemu:///session
> En este stack (homelab de verdad) se usa **`qemu:///system`**: daemon global, VMs propiedad del sistema, persisten al cerrar sesión, red NAT `192.168.122.x` y SSH funcional desde el host. `qemu:///session` es solo para pruebas rápidas.

---

## Paso 4: Verificación del sistema

```bash
# 4.1 KVM cargado (Intel)
lsmod | grep kvm
# kvm_intel  294912  3  /  kvm 1159168  1 kvm_intel
# (En AMD: kvm_amd …)

# 4.2 Daemon activo
sudo systemctl status libvirtd
# Active: active (running)

# 4.3 Conectividad virsh (vacío = normal)
virsh list --all

# 4.4 virt-install
virt-install --version
# virt-install 4.1.0 (o similar)

# 4.5 GUI + viewer
virt-manager &
# Cerrar: killall virt-manager
```

> [!bug] Error "Cannot connect":
> ```bash
> sudo systemctl restart libvirtd
> newgrp libvirt   # o cerrar/reabrir sesión
> virsh list --all
> ```

---

## Paso 5: Descargar y verificar el ISO

### 5.1 Crear estructura de carpetas

```bash
mkdir -p ~/VMs/isos
mkdir -p ~/VMs/disks
cd ~/VMs/isos
```

### 5.2 Selección de versión

| Versión | Tamaño | Caso de uso |
|---------|--------|-------------|
| **netinst** | ~750 MB | Instalador mínimo, descarga paquetes bajo demanda (**recomendado**) |
| **DVD** | ~4.7 GB | Todo en disco (útil sin internet al instalar) |
| **BD** | ~50 GB | Blu-ray (innecesario) |

**Debian (recomendado para el primer homelab):**
```bash
wget https://cdimage.debian.org/cdimage/release/13.7.0/amd64/iso-cd/debian-13.7.0-amd64-netinst.iso
```

Alternativa: Ubuntu Server 22.04 LTS (`https://releases.ubuntu.com/jammy/ubuntu-22.04-live-server-amd64.iso`).

### 5.3 Verificar integridad y autenticidad

```bash
# 1) Verificar que no se corrompió (hash SHA256)
wget https://cdimage.debian.org/cdimage/release/13.7.0/amd64/iso-cd/SHA256SUMS
wget https://cdimage.debian.org/cdimage/release/13.7.0/amd64/iso-cd/SHA256SUMS.asc
sha256sum -c SHA256SUMS --ignore-missing
# debian-13.7.0-amd64-netinst.iso: OK   (FAILED = dañado, re-descargar)

# 2) Verificar que es auténtico (firma de Debian)
gpg --keyserver keyring.debian.org --recv-keys 6D33866EDD8FFA41C0B3D80E8ECF0628648525F4
gpg --verify SHA256SUMS.asc SHA256SUMS
# "Good signature from Debian CD signing key" (el warning de certificación es normal)
```

```bash
ls -lh ~/VMs/isos/
# -rw-r--r-- ... debian-13.7.0-amd64-netinst.iso
```

---

## Paso 6: Crear la primera VM

### Opción A: virt-install (CLI)

```bash
sudo virt-install \
  --name debian-lab \
  --memory 2048 \
  --vcpus 2 \
  --disk path=$HOME/VMs/disks/debian-lab.qcow2,size=20 \
  --cdrom $HOME/VMs/isos/debian-13.7.0-amd64-netinst.iso \
  --os-variant debian13 \
  --network default \
  --graphics spice \
  --console pty,target_type=virtio
```

| Parámetro | Valor | Significado |
|-----------|-------|-------------|
| `--name` | `debian-lab` | Nombre de la VM |
| `--memory` | `2048` | RAM en MB (2 GB — mínimo con Debian GUI) |
| `--vcpus` | `2` | 2 CPUs virtuales |
| `--disk path=…,size=20` | `$HOME/VMs/disks/…,size=20` | Disco QCOW2 de 20 GB |
| `--cdrom` | Ruta al ISO | ISO de instalación |
| `--os-variant` | `debian13` | Config optimizada del SO |
| `--network` | `default` | Red NAT `192.168.122.x` (SSH desde host) |
| `--graphics spice` | — | SPICE (mejor que VNC) |

Qué ocurre: crea el disco → inicia QEMU → abre el instalador de Debian → procedes como instalación normal.

### Opción B: virt-manager (GUI)

1. `virt-manager &` → "+" (Create a new virtual machine)
2. "Local install media (ISO image or CDROM)" → Browse → seleccionar ISO
3. Configurar: **Memory:** 2048 MB · **CPUs:** 2
4. Almacenamiento: "Create a disk image" → **20** GB
5. Red: **"Virtual network 'default' : NAT"**
6. Nombre: `debian-lab` → Finish

---

## Paso 7: Instalación del SO (Debian)

1. **Idioma y teclado** (ej: Spanish)
2. **Red:** DHCP automático (anotar IP, ej: `192.168.122.100`)
3. **Hostname:** `debian-lab` (sin espacios) · **Dominio:** vacío (`.local` default)
4. **Contraseña de root:** fuerte y confirmada
5. **Usuario normal** (ej: `wilk`) + contraseña
6. **Zona horaria** (ej: America/Bogota)
7. **Particionamiento:** "Guided - use entire disk", **sin** LVM ni cifrado (simple para empezar)
8. **Software:** dejarlo mínimo o marcar "OpenSSH server" para SSH
9. **GRUB:** instalar en `/dev/vda`
10. **Instalación:** 10–15 min → reinicio automático

> [!tip] Interacción con la VM
> Clic en la VM **captura** mouse/teclado; **Ctrl+Alt izquierdo** lo libera. Si no funciona el mouse, usar `Tab`.

---

## Paso 8: Configuración post-instalación

### Obtener la IP desde el host
```bash
virsh domifaddr debian-lab
# vnet0  52:54:00:12:34:56  192.168.122.100/24
```

### Acceder a la VM
| Método | Comando |
|--------|---------|
| Consola gráfica | `virt-viewer debian-lab` |
| SSH (recomendado) | `ssh wilk@192.168.122.100` |
| Consola serie | `virsh console debian-lab` (salir: `Ctrl + ]`) |

### Si `sudo` no existe (netinst es mínimo)
```bash
# Dentro de la VM
su -                      # contraseña de root
apt update && apt install -y sudo
usermod -aG sudo wilk     # reemplazar con tu usuario
exit                      # salir de root y de SSH, reconectar
sudo whoami               # → root
```

### Actualizar e instalar herramientas útiles
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y curl wget git htop neofetch openssh-server
```

---

## Paso 9: Operación diaria

```bash
virsh list --all                     # Todas las VMs
virsh start debian-lab               # Encender
virsh shutdown debian-lab            # Apagar ordenado
virsh destroy debian-lab             # Forzar apagado (último recurso)
virsh dominfo debian-lab             # Detalle (CPU, RAM, estado)
virsh domstats debian-lab            # Recursos en tiempo real

virsh snapshot-create-as debian-lab snap1 "Debian limpio con SSH"   # Snapshot base
virsh snapshot-list debian-lab
virsh snapshot-revert debian-lab snap1
```

> [!tip] Un snapshot base al terminar la instalación
> Crea un snapshot del estado "limpio" antes de instalar software experimental: podrás volver atrás sin reinstalar.

---

## Checklist de verificación

- [ ] CPU soporta virtualización (`grep vmx/svm /proc/cpuinfo`)
- [ ] Paquetes instalados (`dpkg -l | grep -E 'qemu|libvirt|virt'`)
- [ ] Permisos configurados (`groups` incluye `kvm` y `libvirt`)
- [ ] KVM cargado (`lsmod | grep kvm`)
- [ ] libvirtd corriendo (`sudo systemctl status libvirtd`)
- [ ] virsh conecta (`virsh list --all`)
- [ ] ISO descargado y verificado (`ls ~/VMs/isos/` + hash OK)
- [ ] VM creada (`virsh list --all`)
- [ ] Debian instalado (acceso a consola)
- [ ] SSH funcionando desde el host
- [ ] sudo configurado
- [ ] Sistema actualizado
- [ ] Snapshot base creado

---

## Troubleshooting

| Error | Solución |
|-------|----------|
| "Cannot connect to libvirt" | `sudo systemctl restart libvirtd` + `newgrp libvirt` (ó cerrar/reabrir sesión) |
| Error de librerías QEMU | `sudo apt install --fix-missing libvirt-clients` |
| VM muy lenta | KVM no se usa: `virsh domstats debian-lab --vcpu`; si `vcpu.time` = 0, revisar `grep vmx/svm` y BIOS |
| Sin acceso gráfico en la VM | Normal en netinst; instalar GUI dentro de la VM |
| No conecta SSH | `virsh list` (¿corre?), `virsh domifaddr`, `ping 192.168.122.100`, `virsh start debian-lab` |
| Disco lleno en la VM | Dentro de la VM: `df -h`, `sudo apt clean && sudo apt autoclean`, `du -sh /*` |

---

## Próximos pasos

1. Crear 2–3 VMs más para un mini-cluster
2. Redes personalizadas con puentes (bridges)
3. Automatizar la creación con scripts bash o Terraform
4. Aprender Ansible / cloud-init para provisioning automático
5. Laboratorios de ciberseguridad (HackTheBox) usando snapshots para revertir
6. Implementar IaC (Infrastructure as Code)

## Referencia

- [[40 - RECURSOS/MOCs/MOC - Virtualización]] — mapa completo del dominio
- [[40 - RECURSOS/Conceptos/Virtualización]] — teoría, stack y comandos de cada herramienta
- [[40 - RECURSOS/MOCs/MOC - Docker]] — containerización como alternativa
- [[40 - RECURSOS/MOCs/MOC - DevSecOps]] — contexto de seguridad de los laboratorios

#virtualización #instalación #kvm #qemu #libvirt #vm #debian