---
type: concepto
state: activa
created: 2026-08-23
updated: 2026-08-23
tags: [seguridad, oauth, owasp, autenticacion, csrf, redirect, token, sso]
aliases: [Vulnerabilidades OAuth]
---

# Vulnerabilidades en OAuth 2.0

OAuth 2.0 se ha convertido en la solución común para SSO (Single Sign-On) con cuentas de redes sociales. Sin embargo, **la especificación del protocolo es relativamente vaga y flexible por diseño**, dejando la gran mayoría de la configuración como opcional y dependiendo casi por completo de que los desarrolladores implementen sus propias medidas de seguridad adicionales.

---

## I. Vulnerabilidades en la Aplicación Cliente

Fallan por errores de configuración o lógica en la app que solicita acceso a datos del usuario.

### 1. Flujo Implícito (Implicit Grant Type)

El token de acceso se envía desde el servicio OAuth al cliente a través del navegador como fragmento de URL, accedido vía JavaScript.

**El fallo lógico:** Para mantener la sesión activa, la app cliente envía token + ID de usuario al servidor vía POST, y el servidor asigna cookie de sesión.

**La vulnerabilidad:** El servidor no posee un secreto con el cual comparar los datos recibidos. Si el backend no valida estrictamente que el token de acceso **coincide con el ID de usuario** en la misma solicitud, un atacante puede interceptar la petición POST y alterar el ID de usuario para suplantar a cualquier víctima.

### 2. Protección CSRF Deficiente (Parámetro `state`)

El parámetro `state` contiene un valor único, aleatorio e impredecible vinculado a la sesión del usuario cuando inicia el flujo. Funciona como token CSRF para OAuth.

**La vulnerabilidad:** Si la solicitud de autorización inicial no envía `state`, el sistema carece de protección CSRF.

**El impacto:**
```
1. Atacante inicia flujo OAuth legítimo con sus credenciales
2. Captura respuesta del servidor de autorización
3. Engaña al navegador de la víctima para que finalice el flujo
4. Si el sitio permite vincular cuenta local con red social:
   → Atacante vincula SU red social al perfil de la víctima
   → Acceso completo a cuenta de víctima
```

---

## II. Vulnerabilidades en el Servicio de OAuth

Fallan en las políticas de validación y configuraciones del servidor de autorización.

### 3. Filtración de Códigos y Tokens vía `redirect_uri`

El código/token de la víctima se envía al endpoint de callback especificado en `redirect_uri`. Si el servidor OAuth permite desviar este parámetro a dominio externo:

**Explotación en flujo de código de autorización:**
```
1. Atacante construye enlace con redirect_uri a su dominio
2. Navegador de víctima inicia flujo OAuth
3. Código/token llega a servidor del atacante
4. Atacante envía código al endpoint legítimo de callback
5. Backend intercambia código por token (sin client secret)
6. → Acceso a cuenta de víctima sin conocer credenciales
```

### 4. Evasión de Validación de `redirect_uri`

Los servidores OAuth registran listas blancas de URIs válidas, pero los atacantes evaden la validación con:

| Técnica | Mecanismo |
|---------|-----------|
| **Validación de prefijos débil** | Solo verifica que comience con dominio aprobado — permite parámetros arbitrarios o rutas adicionales |
| **Discrepancias de parsing URL** | `https://default-host.com &@foo.evil-user.net#@bar.evil-user.net/` confunde al analizador |
| **Contaminación de parámetros** | `?redirect_uri=client.com/callback&redirect_uri=evil.net` — servidor valida uno, redirige con otro |
| **Tratamiento de localhost** | Regla de desarrollo (`localhost`) activa en producción → `localhost.evil-user.net` la evad |
| **Modo de respuesta alterado** | Cambiar `response_mode` de `query` a `fragment` o `web_message` altera el parsing |

### 5. Robo vía Páginas Intermedias (Proxies)

Si no es posible apuntar a dominio externo, el atacante redirige a páginas vulnerables dentro del dominio aprobado:

| Técnica | Mecanismo |
|---------|-----------|
| **Directory Traversal** | `../` para navegar a otros directorios del servidor legítimo |
| **Open Redirect** | Redireccionador abierto en dominio aprobado desvía código/token a dominio del atacante |
| **JS inseguro** | Scripts de mensajería web manipulados para transferir token al atacante |
| **XSS** | Roba código/token de OAuth en página intermedia — aunque cookies tengan HTTPOnly, el token permite login desde otro navegador |
| **HTML Injection** | `<img src="evil.net">` — Firefox envía URL completa (con token) vía cabecera `Referer` |

### 6. Abuso de Validación de Alcances (Scope Upgrade)

El alcance define qué datos puede leer la app cliente. Atacantes escalan privilegios forzando aumento de scopes sin consentimiento:

**En flujo de código de autorización:**
```
1. Atacante registra app maliciosa, solicita scope básico: openid email
2. Usuario otorga consentimiento
3. Atacante intercepta POST de intercambio de token
4. Añade scope adicional: profile
5. Si servidor no valida contra consentimiento original
6. → Token generado con privilegios elevados sin consentimiento
```

**En flujo implícito:**
```
1. Atacante roba token legítimo de app inocente
2. Envía solicitud manual a /userinfo con scope más amplio
3. Si servidor no valida alcance contra asignado originalmente
4. → Entrega información confidencial adicional de víctima
```

### 7. Registro de Usuarios No Verificados

Algunos proveedores OAuth permiten cuentas con correos no validados:

```
1. Atacante registra cuenta en proveedor OAuth con correo de víctima
2. App cliente confía ciegamente en correo del proveedor
3. Atacante inicia sesión en cuenta de usuario legítimo
```

> [!warning] La app no puede asumir veracidad
> La información del proveedor OAuth no está garantizada si no se verificó. Siempre validar independientemente los datos críticos (correo, identidad).

---

## III. Reconocimiento de Superficie de Ataque

Para auditar implementación OAuth, solicitar endpoints estándar del servidor de autorización:

```
GET /.well-known/oauth-authorization-server
GET /.well-known/openid-configuration
```

Devuelven JSON con todas las características, flujos admitidos, parámetros y endpoints disponibles — revelando la superficie de ataque real.

---

## Prevención

| Medida | Detalle |
|--------|---------|
| **Parámetro `state`** | Siempre requerir y validar con valor aleatorio de alta entropía |
| **Lista blanca de `redirect_uri`** | Validación estricta exacta, sin comodines, sin traversal, sin localhost en producción |
| **Authorization Code con PKCE** | Reemplazar Implicit Grant en todas las apps (cliente-servidor y SPA) |
| **Validación backchannel** | Intercambiar código por token solo desde servidor backend, nunca desde navegador |
| **Re-validación de scopes** | Servidor debe validar que scopes en intercambio POST coinciden exactamente con los autorizados |
| **Tokens de corta vida** | Access tokens de 5-15 min, refresh tokens con rotación |
| **Verificación de email** | Requerir validación de correo antes de permitir login vía OAuth |
| **Consentimiento explícito** | No asumir scopes adicionales sin re-prompt al usuario |

---

## Referencia
- [[40 - RECURSOS/Conceptos/Autenticación-y-Autorización]]
- [[40 - RECURSOS/MOCs/MOC - Seguridad]]
- [[40 - RECURSOS/Conceptos/CORS]]
- [[40 - RECURSOS/Conceptos/Anti-Patrones-Seguridad]]

#seguridad #oauth #owasp #csrf #redirect #token #sso
