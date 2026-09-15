---
type: concepto
state: activa
priority: alta
created: 2026-09-13
updated: 2026-09-14
related: [MOC - Virtualización, Guía - Instalación Virtualización]
tags: [virtualización, vm, hipervisor, kvm, qemu, libvirt, virsh, virt-manager, linux, lvm]
aliases: [Virtualización, Máquinas Virtuales, Hipervisor, Stack Virtualización Linux, KVM/QEMU]
---

# Virtualización

## QUÉ es

La **virtualización** es la capacidad de ejecutar múltiples sistemas operativos completos e independientes (**máquinas virtuales**) sobre un único hardware físico.

### Analogía

Una casa (host) con varios apartamentos (VMs):
- Cada apartamento tiene su propia puerta (consola)
- Cada apartamento tiene su propio sistema eléctrico (recursos CPU/RAM)
- Si un apartamento se incendia, los demás no se afectan (aislamiento)
- Todos comparten el mismo terreno (hardware físico)

### Ventajas principales

| Ventaja | Explicación |
|---------|-------------|
| **Aislamiento** | Un crash en una VM no afecta a las demás |
| **Eficiencia** | Mejor uso de CPU, RAM y almacenamiento |
| **Flexibilidad** | Probar múltiples SOs sin dual boot |
| **Portabilidad** | Una VM se puede mover entre hosts físicos |
| **Seguridad** | Testing de malware en entorno aislado |
| **Recuperación** | Snapshots = volver atrás si algo sale mal |

### Conceptos clave

| Concepto | Definición |
|----------|-----------|
| **Host** | Máquina física (CPU, RAM, disco real) que corre el SO principal |
| **Hipervisor** | Software que crea y gestiona las VMs (QEMU dirigido por libvirt) |
| **Guest / Máquina Virtual** | El SO que corre dentro del hipervisor |
| **Recursos asignados** | CPU cores, RAM, disco y red dados a cada VM |

---

## Tipos de virtualización

### 1. Emulación (Full Virtualization)

El hipervisor **traduce completamente cada instrucción** del SO guest al hardware real.

```
1. El SO guest dice: "Quiero sumar 2 + 2"
2. QEMU intercepta esa instrucción
3. QEMU la traduce a código x86 real
4. El CPU ejecuta la instrucción
5. El resultado vuelve al SO guest
```

| Aspecto | Valor |
|---------|-------|
| Velocidad | 30–50% del nativo |
| Requisito de CPU | Cualquiera (emula cualquier arquitectura: ARM en x86, etc.) |
| Compatibilidad SO | Máxima |
| Caso de uso | Testing de arquitecturas raras, educación, CPU sin VT-x/AMD-V |

**Desventaja:** muy lenta por el overhead de traducción de cada instrucción.

### 2. Virtualización asistida por hardware (Hardware-Assisted)

El **CPU moderno tiene instrucciones especiales** (Intel VT-x / AMD-V) que aceleran la virtualización; QEMU casi no traduce.

```
1. El SO guest dice: "Quiero sumar 2 + 2"
2. QEMU lo envía a KVM
3. KVM le dice al CPU: "Ejecuta esto en modo virtual"
4. El CPU ejecuta la instrucción directamente (¡sin traducción!)
5. El resultado vuelve a la VM
```

| Aspecto | Valor |
|---------|-------|
| Velocidad | 95–99% del nativo |
| Requisito de CPU | Intel VT-x o AMD-V (**i5/i7+ o Ryzen+**) |
| Compatibilidad SO | Linux, Windows, macOS, etc. |
| Caso de uso | Producción, homelab, laboratorios de ciberseguridad |

### Comparación

| Aspecto | Emulación (QEMU solo) | Hardware-Assisted (QEMU+KVM) |
|--------|------------------------|------------------------|
| **Velocidad** | 30–50% del nativo | 95–99% del nativo |
| **CPU requerido** | Cualquiera | Intel VT-x o AMD-V |
| **Instalación** | Simple | Un poco más compleja |
| **Casos de uso** | Testing, educación | Producción, homelab |
| **Configuración** | `qemu-system-x86_64 …` | libvirt + QEMU |

> [!tip] Por qué KVM/QEMU y no VirtualBox
> KVM usa la virtualización real del CPU (más rápido, modular, expone cómo funciona). VirtualBox es monolítico y más simple pero lento. KVM es el estándar de producción en Linux.

---

## Stack de virtualización en Linux

```
┌────────────────────────────────────────────────┐
│ Nivel 6: TÚ (usando la VM)                     │
│          ↓ escribes comandos, navegas en GUI
├────────────────────────────────────────────────┤
│ Nivel 5: virt-manager (GUI) o virsh (terminal) │
│          ↓ traduce tus acciones a órdenes
├────────────────────────────────────────────────┤
│ Nivel 4: libvirt (daemon central)              │
│          ↓ gestiona las VMs, controla QEMU
├────────────────────────────────────────────────┤
│ Nivel 3: QEMU (emulador/virtualizador)         │
│          ↓ ejecuta la VM, maneja recursos
├────────────────────────────────────────────────┤
│ Nivel 2: KVM (acelerador del kernel)           │
│          ↓ acelera las instrucciones
├────────────────────────────────────────────────┤
│ Nivel 1: CPU con VT-x/AMD-V                    │
│          ↓ ejecuta la VM a velocidad nativa
├────────────────────────────────────────────────┤
│ Nivel 0: Hardware real (RAM, disco, red)       │
└────────────────────────────────────────────────┘
```

**¿Quién hace qué?**

| Componente | Rol |
|-----------|-----|
| **Tu teclado** | Envías instrucciones |
| **virt-manager / virsh** | Convierte tus comandos en órdenes |
| **libvirt** | "Entiende" las órdenes y las ejecuta |
| **QEMU** | Ejecuta la VM (emula hardware) |
| **KVM** | Acelera la ejecución (usa CPU real) |
| **CPU** | Ejecuta todo a velocidad máxima |

---

## Anatomía del stack

### KVM – Kernel Virtual Machine

**Qué es:** módulo del kernel Linux que proporciona extensiones de virtualización. No es un programa que ejecutes; es parte del SO que actúa de intermediaria entre QEMU y el CPU.

- Viene incluido en el kernel Linux moderno (no requiere instalación)
- Proporciona `/dev/kvm` (archivo especial para comunicarse)
- Usa **Intel VT-x** o **AMD-V**
- Acelera la ejecución de VMs casi nativamente

```bash
# Verificar módulo cargado
lsmod | grep kvm
# Intel:   kvm_intel  294912  3
# AMD:     kvm_amd    176128  3
```

> [!warning] Si `lsmod` no muestra nada
> El CPU no soporta virtualización o está deshabilitado en BIOS. Habilitar "Intel Virtualization Technology"/"VT-x" o "AMD-V"/"SVM" en BIOS y reiniciar.

Relación: KVM solo no hace nada visible · QEMU solo emula lento · **QEMU + KVM** emula rápido.

### QEMU – Quick Emulator

**Qué es:** el emulador/virtualizador real: crea la VM (reserva RAM, disco, red), emula hardware (CPU virtual, teclado, mouse, discos `.qcow2`) y ejecuta el SO guest.

| Paquete | Instala |
|---------|---------|
| `qemu-system-x86` | `/usr/bin/qemu-system-x86_64` (64-bit) y `qemu-system-i386` (32-bit) |

```bash
qemu-system-x86_64 --version
# QEMU emulator version 8.0.0 (Debian 1:8.0.0+dfsg-1)
```

**¿Uso directo?** Posible pero tedioso (tienes que recordar todos los parámetros y nada se guarda). **Por eso existe libvirt**: abstrae la complejidad.

### libvirt – Gestor centralizado

**Qué es:** daemon (`libvirtd`) que gestiona VMs de forma centralizada: guarda la configuración en XML, inicia/detiene QEMU, provee interfaz uniforme (KVM, QEMU, Xen, Hyper-V con los mismos comandos) y gestiona redes, discos y snapshots.

| Paquete | Contenido |
|---------|-----------|
| `libvirt-daemon-system` | Daemon `/usr/sbin/libvirtd` |
| `libvirt-clients` | CLI: `virsh`, `virt-clone`, `virt-xml`, etc. |

**Configuración en XML** (ejemplo de lo que guarda libvirt):

```xml
<domain type='kvm'>
  <name>debian-prueba</name>
  <memory unit='KiB'>2097152</memory>  <!-- 2 GB -->
  <vcpu>2</vcpu>
  <disk type='file'>
    <source file='/home/wilk/VMs/disks/debian-prueba.qcow2'/>
  </disk>
  <interface type='network'>
    <source network='default'/>
  </interface>
</domain>
```

**Dos "mundos" de libvirt:**

| Conexión | `qemu:///system` | `qemu:///session` |
|----------|------------------|-------------------|
| Daemon | Global (requiere sudo) | Por usuario |
| Propiedad de VMs | Sistema (root) | Usuario |
| Persistencia | VMs sobreviven al cerrar sesión | VMs mueren al cerrar sesión |
| Red | `default` NAT en `192.168.122.x` | `user` (slirp), más restrictiva |
| SSH desde host | ✅ Sí | ❌ No |
| Uso | **Homelab de verdad (recomendado)** | Pruebas rápidas |

### Gestión del servicio `libvirtd` (systemctl)

**Ciclo de vida del servicio:**

```bash
sudo systemctl start libvirtd       # iniciar
sudo systemctl stop libvirtd        # detener
sudo systemctl restart libvirtd     # reiniciar
sudo systemctl reload libvirtd      # recargar configuración sin detener
sudo systemctl enable --now libvirtd  # habilitar + iniciar en un comando
sudo systemctl enable libvirtd      # inicio automático
sudo systemctl disable libvirtd     # quitar inicio automático
```

**Verificar estado:**

```bash
sudo systemctl status libvirtd      # estado actual (active / running)
sudo systemctl is-active libvirtd   # solo "active"/"inactive"
sudo systemctl is-enabled libvirtd  # "enabled"/"disabled"
sudo journalctl -u libvirtd -n 50   # últimos logs
sudo journalctl -u libvirtd -f      # logs en tiempo real
```

**Diagnóstico rápido:**

```bash
sudo systemctl show libvirtd        # estado detallado (config efectiva)
ps aux | grep libvirtd              # confirmar proceso corriendo
libvirtd --version                  # versión del daemon
virsh -c qemu:///system list        # probar conexión al socket local
```

> [!bug] Error "Cannot connect to libvirt"
> `sudo systemctl restart libvirtd` y después `newgrp libvirt` (o cerrar/reabrir sesión).

**Permisos para usar libvirt sin sudo:**

```bash
sudo usermod -aG libvirt $USER
sudo usermod -aG kvm $USER
newgrp libvirt
newgrp kvm
id    # verificar pertenencia a los grupos
```

### virsh – Virtual Shell

**Qué es:** CLI para controlar libvirt (viene en `libvirt-clients`). Ejecutable: `/usr/bin/virsh`.

**Listar e información:**

```bash
virsh list                          # solo VMs activas
virsh list --all                    # activas e inactivas
virsh list --all --state-running    # filtro: corriendo
virsh list --all --state-shut-off   # filtro: apagadas
virsh list --all --shutoff          # atajo para apagadas
virsh list --all --running          # atajo para activas
virsh list --all --paused           # atajo para pausadas
virsh dominfo nombre-vm             # información detallada
virsh domstate nombre-vm            # estado actual
virsh domstats nombre-vm            # recursos en tiempo real
```

**Crear VMs:**

```bash
virsh define archivo-definicion.xml        # registrar VM desde XML
virsh create archivo.xml                   # crear y arrancar desde XML
virt-install --name nombre-vm \
  --memory 2048 \
  --vcpus 2 \
  --disk size=20,format=qcow2 \
  --cdrom /ruta/a/iso/ubuntu.iso \
  --os-variant ubuntu22.04 \
  --network default                        # asistente completo
```

**Iniciar, pausar y detener:**

```bash
virsh start nombre-vm               # encender
virsh shutdown nombre-vm            # apagado ordenado (graceful)
virsh reboot nombre-vm              # reiniciar
virsh destroy nombre-vm             # forzar apagado (peligroso)
virsh suspend nombre-vm             # pausar sin apagar
virsh resume nombre-vm              # reanudar pausada
```

**Eliminar:**

```bash
virsh undefine nombre-vm                        # quitar definición (conserva disco)
virsh undefine nombre-vm --remove-all-storage   # quitar definición + discos
virsh undefine nombre-vm --snapshots-metadata   # quitar definición + snapshots
```

**Configuración y consola:**

```bash
virsh dumpxml nombre-vm > vm.xml    # exportar configuración
virsh edit nombre-vm                # editar XML en vivo
virsh domiflist nombre-vm           # interfaces de red de la VM
virsh domblklist nombre-vm          # discos de la VM
virsh console nombre-vm             # consola (salir: Ctrl + ])
virsh domdisplay nombre-vm          # información de display/gráficos
virsh vncdisplay nombre-vm          # puerto VNC (típicamente 5900+)
virsh setmem nombre-vm 4194304      # RAM en vivo (KB) — algunos cambios exigen apagar
virsh setvcpus nombre-vm 4          # CPUs en vivo
```

**Snapshots:**

```bash
virsh snapshot-create-as nombre-vm snapshot-name   # crear
virsh snapshot-list nombre-vm                      # listar
virsh snapshot-revert nombre-vm snapshot-name      # revertir
virsh snapshot-delete nombre-vm snapshot-name      # eliminar
```

**Redes:**

```bash
virsh net-list --all            # listar redes (activas e inactivas)
virsh net-info nombre-red       # información de una red
virsh net-dumpxml nombre-red    # configuración XML de una red
virsh net-start nombre-red      # iniciar red
virsh net-destroy nombre-red    # detener red
virsh net-define archivo-red.xml  # registrar red desde XML
```

**Pools de almacenamiento:**

```bash
virsh pool-list --all           # listar pools
virsh pool-info nombre-pool     # información del pool
virsh pool-dumpxml nombre-pool  # ruta/configuración del pool
virsh pool-create-as nombre-pool dir --target /ruta/a/pool   # crear pool directorio
virsh pool-start nombre-pool    # iniciar pool
virsh pool-autostart nombre-pool  # arranque automático
virsh pool-refresh nombre-pool  # refrescar contenido
virsh vol-list nombre-pool      # listar volúmenes/ discos del pool
virsh vol-create-as nombre-pool nombre-volumen 20G --format qcow2   # crear disco
virsh vol-info /ruta/al/volumen.qcow2   # info de un volumen
virsh vol-delete /ruta/completa/volumen.qcow2  # eliminar volumen
```

**Tips útiles (conexión remota y eventos):**

```bash
virsh -c qemu+ssh://usuario@host/system list     # libvirt remoto vía SSH
virsh -c qemu:///system                          # sistema local (requiere sudo)
virsh -c qemu:///session                         # sesión de usuario (sin sudo)
virsh -c qemu+tcp://host:16509                   # remoto vía TCP
virsh event --all --loop                         # eventos del hipervisor en vivo
cat /etc/libvirt/libvirtd.conf                   # configuración del daemon
```

### Flujo típico completo

```bash
# 1. Verificar que libvirtd esté activo
sudo systemctl status libvirtd

# 2. Listar VMs existentes
virsh list --all

# 3. Crear una VM (ejemplo: Ubuntu)
virt-install --name ubuntu-server \
  --memory 2048 \
  --vcpus 2 \
  --disk size=20,format=qcow2 \
  --cdrom /ruta/a/ubuntu-22.04.iso \
  --network default \
  --os-variant ubuntu22.04

# 4. Ver estado
virsh dominfo ubuntu-server

# 5. Conectar a consola
virsh console ubuntu-server

# 6. Pausar / reanudar
virsh suspend ubuntu-server
virsh resume ubuntu-server

# 7. Apagar gracefully
virsh shutdown ubuntu-server

# 8. Eliminar (definición + discos)
virsh undefine ubuntu-server --remove-all-storage
```

### virt-install – Creación de VMs

**Qué es:** herramienta que automatiza la creación e instalación de VMs desde terminal. Crea la VM, la inicia y abre el instalador. Paquete: `virtinst` → `/usr/bin/virt-install`.

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

| Parámetro | Ejemplo | Significa |
|-----------|---------|-----------|
| `--name` | `debian-lab` | Nombre identificador |
| `--memory` | `2048` | RAM en MB (2048 = 2 GB) |
| `--vcpus` | `2` | CPUs virtuales |
| `--disk path=…,size=20` | `path=$HOME/VMs/disks/debian.qcow2,size=20` | Ruta del disco y tamaño en GB |
| `--cdrom` | `/home/wilk/VMs/isos/debian.iso` | ISO de instalación |
| `--os-variant` | `debian13` | Optimizaciones del SO |
| `--network` | `default` | Red NAT `192.168.122.x` |
| `--graphics` / `--console` | `spice` / `pty,target_type=virtio` | Gráficos y consola |

> [!note] ¿Por qué `sudo`?
> Porque se usa `qemu:///system`, propiedad del sistema (root).

### virt-manager – GUI

**Qué es:** interfaz gráfica para gestionar VMs (crear, instalar, editar, monitorear). Paquete: `virt-manager` → `/usr/bin/virt-manager`. Se inicia con `virt-manager &` (o "Virtual Machine Manager" en el menú). Crea VMs con asistente visual, edita recursos, muestra gráficas de CPU/RAM/I-O y abre la consola con doble-clic. Para consola gráfica dedicada también existe `virt-viewer` (`/usr/bin/virt-viewer`).

---

## Snapshots y puntos de restauración

Los **snapshots** son fotografías del estado de la VM en un momento dado. Ideales para testing.

```bash
virsh snapshot-create-as debian-lab snap1 "Debian limpio con SSH"   # Crear
virsh snapshot-list debian-lab                                       # Listar
virsh snapshot-revert debian-lab snap1                               # Revertir
virsh snapshot-delete debian-lab snap1                               # Eliminar
```

Útil para: probar exploits, instalar software experimental, laboratorios de ciberseguridad.

---

## Tareas avanzadas

| Tarea | Comando |
|-------|---------|
| Ver/editar config XML | `virsh dumpxml debian-lab` / `virsh edit debian-lab` |
| Aumentar RAM en vivo | `virsh setmem debian-lab 4194304` (4 GB = 4194304 KB) |
| Aumentar CPUs en vivo | `virsh setvcpus debian-lab 4` |
| Clonar VM | `virt-clone --original debian-lab --name debian-lab-2 --auto-clone` |
| Exportar/importar VM | `virsh dumpxml debian-lab > lab.xml` → luego `virsh define lab.xml` |

---

## Conceptos adicionales

### Particionamiento

Una **partición** es una división del disco duro (áreas independientes).

| Enfoque | Estructura | Cuándo |
|---------|-----------|--------|
| **Una sola partición `/`** | Todo el SO y datos en una | Principiantes, primer homelab |
| **Múltiples particiones** | `/boot` (kernel), `/` (SO), `/home` (datos), `/var` (logs), `swap` | Producción — aisla fallos y mejora control/seguridad |

### LVM (Logical Volume Manager)

**Qué es:** capa de abstracción que permite **redimensionar particiones sin perder datos** (creces un volumen de 10→20 GB en caliente). LVM + LUKS añade cifrado. Recomendado para producción y laboratorios avanzados; **no necesario en la primera VM**.

---

## Próximos pasos (filosofía homelab)

```
Semana 1-2: Manual (aprendes cada paso)
    ↓ Creas 1-2 VMs a mano
Semana 3-4: Semiautomatizado (scripts bash)
    ↓ Creas VMs más rápido
Semana 5+: Automatizado (Terraform + Ansible / cloud-init)
    ↓ Define VMs como código, reproducibles y versionadas
```

1. Crear 2–3 VMs más (mini-cluster)
2. Redes personalizadas con puentes (bridges)
3. Automatizar con scripts bash o Terraform
4. Aprender Ansible (provisioning) y cloud-init
5. Practicar laboratorios de ciberseguridad (HackTheBox, snapshots para revertir exploits)
6. Implementar IaC

## Referencia

- [[40 - RECURSOS/MOCs/MOC - Virtualización]] — mapa completo del dominio
- [[40 - RECURSOS/Guías & Flujos/Guía - Instalación Virtualización]] — instalación y primera VM paso a paso
- [[40 - RECURSOS/MOCs/MOC - Docker]] — containerización como alternativa a VMs
- [[40 - RECURSOS/MOCs/MOC - Kubernetes]] — orquestación de containers

#virtualización #vm #hipervisor #kvm #qemu #libvirt #lvm #linux