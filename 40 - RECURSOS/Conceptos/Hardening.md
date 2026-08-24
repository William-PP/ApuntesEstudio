---
type: concepto
state: activa
created: 2026-08-15
updated: 2026-08-23
tags: [ciberseguridad, hardening, linux, debian, nist, cis, sysadmin, kernel, firewall]
aliases: [Hardening]
---

# Hardening

Para aplicar hardening en un sistema como Debian 13, seguí un camino estructurado basado en los marcos de **NIST** y **CIS**, complementados con la guía de **Madaidan's Insecurities**. El hardening no es un evento único, sino un proceso continuo de reducción de la superficie de ataque.

## Las 6 fases

| Fase | Tema | Nota |
|------|------|------|
| 1 | Identificación, Inventario y Planificación | [[40 - RECURSOS/Conceptos/Hardening-Fase-1]] |
| 2 | Seguridad Física y Proceso de Arranque | [[40 - RECURSOS/Conceptos/Hardening-Fase-2]] |
| 3 | Hardening del Kernel | [[40 - RECURSOS/Conceptos/Hardening-Fase-3]] |
| 4 | Gestión de Cuentas y Privilegios | [[40 - RECURSOS/Conceptos/Hardening-Fase-4]] |
| 5 | Reducción de Superficie de Ataque (Red y Software) | [[40 - RECURSOS/Conceptos/Hardening-Fase-5]] |
| 6 | Monitoreo Continuo, Parcheo y Recuperación | [[40 - RECURSOS/Conceptos/Hardening-Fase-6]] |

## Deep dives transversales

### Trade-off entre Usabilidad y Seguridad

> [!warning]
> Si las protecciones son demasiado frustrantes, los usuarios buscarán evitarlas o deshabilitarlas. Evaluá qué medidas (bloqueo estricto de puertos, sandboxing) podrían impedir tus tareas diarias.

### La Seguridad como Problema de Diseño

> [!note]
> La seguridad no es una capa que se añade al final. Antes de ejecutar comandos, la parte más ignorada es el **Inventario de Activos y Clasificación de Datos** (CIS Controles 1, 2 y 3). Si no sabés qué software tenés o qué datos son sensibles, aplicarás el mismo esfuerzo a cosas triviales que a cosas críticas.

## ¿Hasta dónde se extiende?

El hardening **no tiene un final definitivo**. Se extiende desde el diseño inicial hasta el desmantelamiento, adaptándose a nuevas amenazas, nuevo software y cambiantes necesidades de usabilidad.

> [!tip] Por dónde empezar
> Enfocate primero en la **higiene esencial (IG1)** de CIS: actualizaciones, contraseñas fuertes, cortafuegos y copias de seguridad. A medida que ganes confianza, profundizá en kernel y políticas de control de acceso mandatorio (AppArmor).

## Checklist básico (IG1)

- [ ] Inventario de activos documentado
- [ ] Datos sensibles identificados
- [ ] Línea base de configuración guardada
- [ ] BIOS/UEFI con contraseña y sin arranque USB
- [ ] Disco cifrado con LUKS
- [ ] GRUB protegido con contraseña
- [ ] Sysctl con restricciones de kernel activas
- [ ] Root bloqueado, uso de `sudo`
- [ ] PAM con políticas de contraseña fuertes
- [ ] Servicios innecesarios eliminados
- [ ] Firewall `nftables` con denegación por defecto
- [ ] `umask 0077` en `/etc/profile`
- [ ] Actualizaciones de seguridad automáticas
- [ ] Logs configurados y revisados
- [ ] Backups automáticos y cifrados

---

## Referencia
- [[40 - RECURSOS/MOCs/MOC - Hardening]]
- [[40 - RECURSOS/MOCs/MOC - Ciberseguridad]]
- [[MOC - DevSecOps]]

#ciberseguridad #hardening #linux #debian #nist #cis #sysadmin #kernel #firewall
