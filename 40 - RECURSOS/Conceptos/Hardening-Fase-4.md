---
type: concepto
state: activa
created: 2026-08-15
updated: 2026-08-23
tags: [hardening, ciberseguridad, cuentas, root, sudo, pam, mfa, privilegios]
aliases: [Hardening Fase 4]
---

# Hardening Fase 4: Gestión de Identidades, Cuentas y Privilegios

La **Fase 4** controla quién puede acceder al sistema y qué acciones está autorizado a realizar, aplicando estrictamente el **principio de menor privilegio**.

## 1. Fortalecimiento de la Cuenta Root

| Acción | Configuración | Detalle |
|--------|---------------|---------|
| **Bloquear root** | `passwd -l root` | Nadie inicia sesión como root directamente |
| **Restricción de terminales** | Vaciar `/etc/securetty` | Impide root desde cualquier terminal |
| **Cuenta administrativa separada** | Crear usuario `admin` + `sudo` | No usar cuenta normal para tareas administrativas |
| **Restricción de `su`** | Grupo `wheel` (o admin) | Limitar switch user a usuarios autorizados |

```bash
# Bloquear login directo de root
passwd -l root

# Vaciar /etc/securetty
> /etc/securetty

# Carpeta personal privada
chmod 700 /home/$user
```

## 2. Gestión de Contraseñas y PAM

El framework **PAM** (Pluggable Authentication Modules) configura políticas de autenticación.

| Acción | Configuración | Efecto |
|--------|---------------|--------|
| **Rondas de hashing** | SHA-512 con 65536 rondas | Fuerza bruta mucho más lenta |
| **Calidad de contraseñas** | `pam_pwquality` con longitud mínima 14-16 | Exige contraseñas robustas |
| **Retardo ante fallos** | `pam_faildelay` con ~4 segundos | Frena intentos automatizados |

## 3. Autenticación Multifactor (MFA)

| Acción | Detalle |
|--------|---------|
| **MFA para acceso administrativo** | Segundo factor obligatorio para cuentas con privilegios |
| **Tokens físicos** | Uno de los factores debe ser dispositivo físico separado |

## 4. Menor Privilegio en Archivos y Binarios

| Acción | Configuración | Efecto |
|--------|---------------|--------|
| **Permisos de home** | `chmod 700 /home/$user` | Restringe lectura entre usuarios |
| **Remoción SUID/SGID** | Buscar y eliminar setuid/setgid innecesarios | Evita escalada de privilegios |
| **Umask** | `umask 0077` en `/etc/profile` | Archivos nuevos privados por defecto |

```bash
# Buscar binarios con bits SUID/SGID
find / -perm -4000 -o -perm -2000 2>/dev/null
```

## 5. Control e Inventario de Cuentas (CIS Control 5)

| Acción | Detalle |
|--------|---------|
| **Inventario de cuentas** | Lista actualizada de todas las cuentas |
| **Cuentas inactivas** | Deshabilitar o eliminar tras 45 días sin actividad |
| **Separación de funciones** | Tareas administrativas desde cuentas dedicadas |

> [!note] Resultado
> Incluso si un atacante obtiene una contraseña, se encontrará con **múltiples capas de protección** que le impedirán moverse lateralmente o escalar privilegios.

---

## Referencia
- [[40 - RECURSOS/Conceptos/Hardening]]
- [[40 - RECURSOS/Conceptos/Hardening-Fase-3]]
- [[40 - RECURSOS/Conceptos/Hardening-Fase-5]]
- [[40 - RECURSOS/MOCs/MOC - Hardening]]

#hardening #cuentas #root #sudo #pam #mfa #privilegios
