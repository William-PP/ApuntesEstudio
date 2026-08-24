---
type: concepto
state: activa
created: 2026-08-15
updated: 2026-08-23
tags: [hardening, ciberseguridad, firewall, nftables, sandboxing, wayland, servicios]
aliases: [Hardening Fase 5]
---

# Hardening Fase 5: Reducción de la Superficie de Ataque (Red y Software)

La **Fase 5** minimiza vectores de entrada disponibles para un atacante, limitando funciones del sistema a lo estrictamente necesario.

## 1. Principio de Menor Funcionalidad

| Acción | Detalle |
|--------|---------|
| **Eliminación de software/servicios** | Desinstalar servicios, puertos y protocolos innecesarios (Samba/NFS, módulos web no utilizados) |
| **Desactivar componentes del kernel** | Bloquear módulos de protocolos poco comunes (`sctp`, `dccp`) y hardware como Bluetooth |
| **Listas permitidas (allowlists)** | Solo apps y librerías autorizadas pueden ejecutarse (NIST/CIS) |

> [!tip] Regla mental
> Si un servicio no se usa, se desinstala o se apaga. Cada servicio de red activo es una puerta más para un atacante.

## 2. Configuración Defensiva de Red

| Acción | Detalle |
|--------|---------|
| **Firewall denegación por defecto** | `nftables` o `iptables` con regla DROP, permitiendo solo tráfico explícitamente autorizado |
| **Filtrado de DNS** | Servicios de filtrado para bloquear dominios de malware/C2 |

```bash
# nftables: política por defecto DROP
nft add table inet filter
nft chain inet filter input { type filter hook input priority 0 ; policy drop ; }
nft chain inet filter forward { type filter hook forward priority 0 ; policy drop ; }
nft add rule inet filter input ct state established,related accept
# Abrir SOLO puertos necesarios, ej. SSH:
nft add rule inet filter input tcp dport 22 accept
```

## 3. Aislamiento de Aplicaciones (Sandboxing)

| Acción | Detalle |
|--------|---------|
| **Sandboxes** | Herramientas como **bubblewrap** o **gVisor** para apps riesgosas |
| **Aislamiento GUI** | Preferir **Wayland** sobre Xorg: aísla ventanas entre sí (previene keylogging) |
| **Prevención de escapes** | Restringir **D-Bus** y **PulseAudio** dentro del sandbox |

## 4. Endurecimiento de Software de Usuario

| Acción | Detalle |
|--------|---------|
| **Navegador y correo** | Solo software con actualizaciones constantes, deshabilitar extensiones innecesarias |
| **Asignador de memoria** | Implementar `hardened_malloc` contra corrupción de heap |
| **Validación de entradas** | Configurar apps para validación estricta de todas las entradas |

> [!note] Resultado
> Tu Debian 13 será **resistente a la intrusión remota**, con cada app en entorno controlado y mínimo de privilegios y conexiones posibles.

> [!warning] Deep Dive: Escapes de Sandbox
> **PulseAudio** y **D-Bus** son vías frecuentes para escapar de entornos aislados. En el entorno gráfico, **Xorg** es intrínsecamente inseguro: cualquier ventana puede registrar lo que escribís en otra (keylogging). Migrar a **Wayland**, que aísla las ventanas por defecto.

---

## Referencia
- [[40 - RECURSOS/Conceptos/Hardening]]
- [[40 - RECURSOS/Conceptos/Hardening-Fase-4]]
- [[40 - RECURSOS/Conceptos/Hardening-Fase-6]]
- [[40 - RECURSOS/MOCs/MOC - Hardening]]

#hardening #firewall #nftables #sandboxing #wayland #servicios
