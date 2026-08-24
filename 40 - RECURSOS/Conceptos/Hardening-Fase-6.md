---
type: concepto
state: activa
created: 2026-08-15
updated: 2026-08-23
tags: [hardening, ciberseguridad, monitoreo, logs, backups, parches, incidentes]
aliases: [Hardening Fase 6]
---

# Hardening Fase 6: Monitoreo Continuo, Parcheo y Recuperación

La **Fase 6** garantiza que el nivel de seguridad se mantenga en el tiempo y que el sistema sea resiliente ante fallos o ataques.

## 1. Gestión Continua de Vulnerabilidades y Parcheo

| Acción | Detalle |
|--------|---------|
| **Remediación de fallos (NIST SI-2)** | Identificar, reportar y corregir fallos instalando actualizaciones en periodos definidos |
| **Parcheo automatizado** | Actualizaciones del SO y apps de forma mensual o más frecuente (CIS) |
| **Microcode de CPU** | Instalar actualizaciones para corregir Spectre, Meltdown |

```bash
apt update && apt upgrade
apt install -y unattended-upgrades
```

## 2. Gestión y Revisión de Logs de Auditoría

| Acción | Detalle |
|--------|---------|
| **Recolección de eventos (NIST AU-2)** | Registrar cambios de contraseñas, accesos fallidos, uso de privilegios admin |
| **Centralización (CIS Control 8)** | Servidor dedicado o SIEM para evitar que atacante modifique logs |
| **Revisión semanal** | Detectar anomalías o eventos anormales |

## 3. Recuperación de Datos y Resiliencia

| Acción | Detalle |
|--------|---------|
| **Copias automatizadas** | Respaldos automáticos con frecuencia según sensibilidad de datos |
| **Protección de backups** | Mismos controles que originales (cifrado) en instancia aislada |
| **Pruebas de restauración** | Probar recuperación trimestralmente |

> [!warning] Backup cifrado
> Una práctica de hardening es inútil si perdés tus datos por un fallo o un ataque y no tenés cómo recuperarlos.

## 4. Respuesta a Incidentes

| Acción | Detalle |
|--------|---------|
| **Plan de Respuesta (NIST RESPOND)** | Detección, análisis, contención, erradicación y recuperación |
| **Lecciones aprendidas** | Revisiones post-incidente para prevenir recurrencia |

## 5. Monitoreo del Sistema (NIST SI-4)

| Acción | Detalle |
|--------|---------|
| **Detección de Intrusiones** | Herramientas para detectar ataques y conexiones no autorizadas |
| **Alertas automáticas** | Notificaciones ante IOC (Indicadores de Compromiso) |

> [!note] Resultado
> Tu Debian 13 contará con un ciclo de **mejora continua**. El hardening se extiende hasta el final de la vida útil del equipo.

> [!warning] Deep Dive: Ceguera de logs
> CIS enfatiza que recolectar logs es solo una parte; la clave es la **correlación de eventos**. NIST sugiere análisis de tendencias y texto completo en comandos privilegiados para detectar comportamientos anómalos.

---

## Referencia
- [[40 - RECURSOS/Conceptos/Hardening]]
- [[40 - RECURSOS/Conceptos/Hardening-Fase-5]]
- [[40 - RECURSOS/MOCs/MOC - Hardening]]
- [[40 - RECURSOS/MOCs/MOC - Monitoreo]]

#hardening #monitoreo #logs #backups #parches #incidentes
