---
type: concepto
state: activa
created: 2026-08-15
updated: 2026-08-23
tags: [hardening, ciberseguridad, kernel, sysctl, modulos, aslr, nist, cis]
aliases: [Hardening Fase 3]
---

# Hardening Fase 3: Hardening del Kernel

La **Fase 3** es uno de los pasos más críticos: el kernel es el binario "setuid root" más grande y vulnerable del sistema. Configurar el kernel para que se proteja a sí mismo reduce su superficie de ataque.

## 1. Autoprotección mediante Sysctl

`sysctl` permite configurar parámetros del kernel en tiempo de ejecución.

| Parámetro | Valor | Efecto |
|-----------|-------|--------|
| `kernel.kptr_restrict` | `2` | Oculta direcciones de memoria del kernel |
| `kernel.dmesg_restrict` | `1` | Impide a usuarios no privilegiados ver log del kernel |
| `kernel.unprivileged_bpf_disabled` | `1` | Deshabilita eBPF no privilegiado |
| `net.core.bpf_jit_harden` | `2` | Endurece JIT de eBPF |
| `fs.protected_symlinks` | `1` | Protege enlaces simbólicos (TOCTOU) |
| `fs.protected_hardlinks` | `1` | Protege enlaces duros |

```bash
# /etc/sysctl.conf
kernel.kptr_restrict = 2
kernel.dmesg_restrict = 1
kernel.unprivileged_bpf_disabled = 1
net.core.bpf_jit_harden = 2
fs.protected_symlinks = 1
fs.protected_hardlinks = 1
net.ipv4.conf.all.rp_filter = 1
net.ipv4.conf.default.rp_filter = 1
```

## 2. Parámetros de Seguridad en el Arranque

Ajustes que solo se aplican al inicio mediante parámetros del cargador (GRUB).

| Parámetro | Efecto |
|-----------|--------|
| `slab_nomerge` | Deshabilita fusión de cachés slab: dificulta corrupción de memoria |
| `init_on_alloc=1` | Memoria llena de ceros al asignar (mitiga fuga de datos) |
| `init_on_free=1` | Memoria llena de ceros al liberar (mitiga use-after-free) |
| `lockdown=confidentiality` | Frontera entre espacio de usuario y kernel |
| `page_alloc.shuffle=1` | Asignaciones de memoria menos predecibles |
| `pti=on` | Mitiga Meltdown |

```text
# Parámetros de arranque en GRUB (GRUB_CMDLINE_LINUX_DEFAULT)
slab_nomerge init_on_alloc=1 init_on_free=1 lockdown=confidentiality page_alloc.shuffle=1 pti=on
```

## 3. Reducción de la Superficie de Ataque

Deshabilitar módulos y protocolos innecesarios.

| Acción | Detalle |
|--------|---------|
| **Lista negra de módulos** | Bloquear `dccp`, `sctp`, `rds`, `tipc` |
| **Desactivar hardware innecesario** | Bluetooth, cámaras (`uvcvideo`), sistemas de archivos poco comunes (`cifs`, `nfs`, `gfs2`) |
| **Restricción de namespaces** | `kernel.unprivileged_userns_clone=0` |

```bash
# /etc/modprobe.d/blacklist-hardening.conf
blacklist dccp
blacklist sctp
blacklist rds
blacklist tipc
blacklist uvcvideo
blacklist bluetooth
blacklist cifs
blacklist nfs
blacklist gfs2
```

## 4. Alineación con NIST y CIS

| Control | Referencia | Qué busca |
|---------|-----------|-----------|
| **Aislamiento de funciones** | NIST SC-3 | Aislar funciones de seguridad del kernel de las de no seguridad |
| **Integridad de Software** | NIST SI-7 | Firmas digitales para módulos (`module.sig_enforce=1`) |
| **Configuración Segura** | CIS Control 4 | Evitar configuraciones por defecto y aplicar benchmarks |

> [!warning] Probar antes de aplicar
> Cambios de kernel mal aplicados pueden impedir el arranque. Aplicá con `sysctl -p`, probá un reinicio y mantené la línea base para revertir.

> [!note] Resultado
> El kernel de tu Debian 13 será más difícil de comprometer y tendrá mecanismos para **detectar y fallar de manera segura** ante intentos de explotación.

> [!warning] Deep Dive: Selección del kernel
>
> | Opción | Ventaja | Riesgo |
> |--------|---------|--------|
> | **Kernel LTS** | Soporte prolongado y estabilidad | Parches más atrasados |
> | **Kernel estable** | Parches más recientes | Nuevas funciones = más superficie |
> | **Kernel compilado a medida** | Desactiva módulos innecesarios | Mayor mantenimiento |

---

## Referencia
- [[40 - RECURSOS/Conceptos/Hardening]]
- [[40 - RECURSOS/Conceptos/Hardening-Fase-2]]
- [[40 - RECURSOS/Conceptos/Hardening-Fase-4]]
- [[40 - RECURSOS/MOCs/MOC - Hardening]]

#hardening #kernel #sysctl #modulos #nist #cis
