# Kerberos y SAM

## QUÉ es cada uno

### Kerberos

El "sistema de pases VIP" de una red empresarial (**Active Directory**). En vez de que cada PC guarde las claves, un servidor central (**KDC**) las gestiona. Permite identificarse ante múltiples servicios usando **Tickets**, así la contraseña no viaja por el cable constantemente.

### SAM (Security Account Manager)

El archivo local de Windows que actúa como "llavero" de cada equipo. Guarda usuarios y contraseñas (como **hashes**) de quienes pueden entrar a esa máquina sin necesidad de red.

## Ataques y detección

| Concepto | Ataque / Proceso | Filtro Wireshark | Evidencia técnica | Finalidad |
|----------|-----------------|-------------------|-------------------|-----------|
| **AD (Dominio)** | Brute Force | `kerberos.error_code == 24` | Ráfaga de KRB_ERROR con KDC_ERR_PREAUTH_FAILED | Adivinar contraseña |
| **AD (Dominio)** | User Enumeration | `kerberos.error_code == 6` | KRB_ERROR con KDC_ERR_C_PRINCIPAL_UNKNOWN | Listar usuarios válidos |
| **AD (Dominio)** | AS-REP Roasting | `kerberos.msg_type == 10` | AS-REQ sin campo padata (pre-auth) | Robar hashes sin dejar rastro |
| **AD (Dominio)** | Kerberoasting | `kerberos.msg_type == 12` | Muchas TGS_REQ hacia servicios (MSSQLSvc, HTTP) | Obtener hashes de cuentas de servicio |
| **AD / Local** | Uso de NTLM | `ntlmssp` | Paquetes CHALLENGE y AUTHENTICATE | Suplantación vía Relay |
| **AD (Dominio)** | Login exitoso | `kerberos.msg_type == 11` | AS-REP con campo ticket cifrado (AES-256) | Confirmar TGT obtenido |

## Referencia
- [[Ciberseguridad/00-Index]]
- [[DNS/DNS-Sospechoso]]
- [[Redes/Filtros-Wireshark]]
