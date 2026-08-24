---
type: concepto
state: activa
created: 2026-08-23
updated: 2026-08-23
tags: [seguridad, oauth, owasp, autenticacion, csrf, redirect, token]
aliases: [Vulnerabilidades OAuth]
---

# Vulnerabilidades en OAuth 2.0

OAuth 2.0 permite delegar acceso sin revelar credenciales al cliente, pero su flexibilidad arquitectónica y la ausencia de características de seguridad obligatorias facilitan fallas graves de implementación.

## 1. Flujo Implícito (Implicit Grant Type)

El token de acceso se transmite por fragmento de URL en el navegador. Si la aplicación envía este token junto con el identificador del usuario al servidor vía POST posterior, y el servidor los acepta sin validar por backchannel si el token corresponde al usuario indicado, un atacante puede alterar los parámetros y suplantar a cualquier víctima.

## 2. Ausencia del Parámetro `state`

El parámetro `state` actúa como token CSRF dentro del flujo OAuth. Su ausencia o validación incorrecta permite:

- Iniciar un flujo OAuth propio
- Engañar a la víctima para que lo complete
- Vinculación forzada de perfiles (asociar cuenta de víctima con perfil del atacante)
- CSRF de inicio de sesión

## 3. Fugas vía `redirect_uri`

El servidor de OAuth envía el código/token al endpoint de retorno de la app cliente. Si no valida estrictamente esta dirección contra una lista blanca fija:

| Técnica | Mecanismo |
|---------|-----------|
| **Dominio malicioso** | Atacante construye enlace que envía código a su dominio |
| **Directory traversal** | `../` para evadir listas blancas |
| **Contaminación de parámetros** | Parámetros adicionales en URL compleja |
| **Discrepancias de interpretación** | Diferencias en parsing de URLs entre componentes |

## 4. Páginas Intermedias (Proxies)

Si el atacante no puede apuntar a dominio externo, redirige a páginas vulnerables dentro del dominio permitido:

- **Open redirect** en el mismo dominio
- **Scripts JS vulnerables** de mensajería
- **Inyección HTML** que filtra token vía cabecera `Referer`
- **XSS** para interceptar y transferir credenciales de sesión

## 5. Abuso de Validación de Alcances (Scope Upgrade)

### En flujo de código de autorización

Atacante registra app legítima pero solicita alcances adicionales (acceso completo al perfil) durante el intercambio POST posterior al consentimiento. Si el servidor es vulnerable, genera el token con los nuevos privilegios **sin consentimiento explícito**.

### En flujo implícito

Atacante agrega manualmente parámetros de alcances superiores directamente en las llamadas a la API de la víctima.

## 6. Registro de Usuarios No Verificados

Si el proveedor OAuth permite cuentas con correos no validados:

1. Atacante crea perfil en proveedor con correo de la víctima
2. App cliente confía a ciegas en datos del proveedor
3. Atacante inicia sesión en cuenta del usuario legítimo

---

## Prevención

| Medida | Detalle |
|--------|---------|
| **Parámetro `state`** | Siempre requerir y validar state con valor aleatorio de alta entropía |
| **Lista blanca de `redirect_uri`** | Validación estricta, sin comodines, sin traversal |
| **Flujo de código (no implícito)** | Usar Authorization Code con PKCE en vez de Implicit Grant |
| **Validación backchannel** | Intercambiar código por token solo desde servidor backend |
| **Tokens de corta vida** | Access tokens de 5-15 min, refresh tokens con rotación |
| **Verificación de email** | Requerir validación de correo antes de permitir login |
| **Consentimiento explícito** | No asumir scopes adicionales sin re-prompt al usuario |

---

## Referencia
- [[40 - RECURSOS/Conceptos/Autenticación-y-Autorización]]
- [[40 - RECURSOS/MOCs/MOC - Seguridad]]
- [[40 - RECURSOS/Conceptos/CORS]]
- [[40 - RECURSOS/Conceptos/Anti-Patrones-Seguridad]]

#seguridad #oauth #owasp #csrf #redirect #token
