---
type: concepto
state: activa
created: 2026-08-15
updated: 2026-08-23
tags: [hardening, ciberseguridad, bios, uefi, luks, grub, secure-boot, cifrado]
aliases: [Hardening Fase 2]
---

# Hardening Fase 2: Seguridad Física y del Proceso de Arranque

La **Fase 2** protege el sistema antes de que el SO se haya cargado por completo, evitando que un atacante con acceso físico comprometa la máquina.

## 1. Hardening de BIOS/UEFI y Firmware

El firmware es el primer código que se ejecuta al encender.

| Acción | Detalle |
|--------|---------|
| **Contraseña de Firmware** | Establecer contraseña fuerte en BIOS/UEFI para evitar cambios de configuración de arranque |
| **Deshabilitar Arranque Externo** | Desactivar arranque desde USB, CD o red: permite iniciar SO propio y saltarse protecciones |
| **Actualizaciones de Microcode** | Mantener firmware y microcode de CPU actualizados (Spectre, Meltdown) |

## 2. Cifrado de Disco Completo (FDE)

Cifrar datos en reposo con **dm-crypt/LUKS** para que sean ilegibles si el equipo es robado.

| Acción | Detalle |
|--------|---------|
| **Implementación de LUKS** | Usar dm-crypt/LUKS durante instalación para cifrar todas las particiones de datos |
| **Limitaciones** | FDE por lo general **no cubre `/boot`**: kernel y cargador quedan expuestos |

> [!warning] /boot sin cifrar
> El cifrado completo no protege `/boot`. Por eso la Fase 2 combina FDE con contraseña de GRUB y Secure Boot: cada medida cubre la debilidad de la otra.

## 3. Protección del Cargador de Arranque (GRUB)

El cargador es un punto crítico donde un atacante físico puede inyectar parámetros para obtener root.

| Acción | Detalle |
|--------|---------|
| **Contraseña de GRUB** | Evita editar entradas del menú (presionando `e`) para añadir `init=/bin/bash` |
| **Configuración en Debian** | Usar `grub-mkpasswd-pbkdf2` para generar hash seguro y añadirlo a configuración |

```bash
# Generar hash PBKDF2
grub-mkpasswd-pbkdf2

# Agregar hash a /etc/grub.d/00_header y regenerar
update-grub
```

## 4. Arranque Verificado (Verified Boot)

Cada componente del proceso de inicio se verifica criptográficamente antes de ejecutarse.

| Acción | Detalle |
|--------|---------|
| **UEFI Secure Boot** | Verifica firma digital del cargador de arranque y del kernel |
| **Cadena de Confianza** | Si un componente fue alterado, el sistema se niega a arrancar |

## 5. Mitigación de Ataques Físicos Avanzados

| Acción | Detalle |
|--------|---------|
| **Ataques DMA** | Deshabilitar Thunderbolt/FireWire si no se usan: pueden leer RAM directamente |
| **Protección IOMMU** | Habilitar `intel_iommu=on` o `amd_iommu=on` en parámetros de arranque |
| **Seguridad USB** | Bloquear puertos no utilizados o usar **USBGuard** para dispositivos autorizados |

```text
# Parámetros de arranque en GRUB (GRUB_CMDLINE_LINUX_DEFAULT)
intel_iommu=on    # o amd_iommu=on
```

## 6. Control de Acceso Físico (NIST PE-3)

| Acción | Detalle |
|--------|---------|
| **Restricción de Acceso** | Limitar quién puede estar cerca del equipo físico |
| **Protección contra manipulación** | Carcasas con candado o sellos de evidencia de manipulación |

> [!note] Resultado
> Al completar esta fase, habrás establecido una **raíz de confianza** sólida: solo el software autorizado puede ejecutarse y tus datos están protegidos contra acceso físico no autorizado.

> [!warning] Deep Dive: /boot sin cifrar
> El cifrado estándar **no suele cubrir `/boot`**, lo que permite que un atacante físico modifique el kernel o el gestor de arranque antes de que el sistema se cifre. Para mitigarlo, investigá **Verified Boot** (Arranque Verificado), que asegura criptográficamente que ningún componente haya sido alterado.

---

## Referencia
- [[40 - RECURSOS/Conceptos/Hardening]]
- [[40 - RECURSOS/Conceptos/Hardening-Fase-1]]
- [[40 - RECURSOS/Conceptos/Hardening-Fase-3]]
- [[40 - RECURSOS/MOCs/MOC - Hardening]]

#hardening #bios #uefi #luks #grub #secure-boot #cifrado
