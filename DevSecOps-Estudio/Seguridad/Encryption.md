# Encryption (Encriptación)

> [!info] OWASP Top 10:2025
> **A04:Cryptographic Failures** — Baja de #2 a #4 pero sigue siendo crítica. Incluye encriptación débil, manejo incorrecto de claves, y falta de cifrado en tránsito/reposo.

## QUÉ es

Proceso de convertir datos legibles en datos ilegibles que solo pueden leerse con una clave o contraseña.

## Tipos de encriptación

| Tipo | Dirección | Clave | Ejemplo de uso |
|------|-----------|-------|----------------|
| **Hash** | Unidireccional (irreversible) | No usa clave, usa contraseña | Almacenar passwords |
| **Symmetric** | Bidireccional | Misma clave para cifrar y descifrar | Datos en BD |
| **Asymmetric** | Bidireccional | Par de claves (pública + privada) | Intercambio de claves, firmas digitales |
| **TLS/SSL** | Bidireccional | Negociación de claves | Comunicación HTTPS |

## POR QUÉ importa

| Sin encriptación | Con encriptación |
|------------------|------------------|
| Passwords en texto plano → filtración total | Passwords hasheados con bcrypt → inútiles sin brute force |
| Datos en tránsito interceptables → MITM | Datos en tránsito cifrados con TLS → ilegibles |
| Secrets en el código → filtración inmediata | Secrets en vault/env vars → controlados |
| Datos sensibles en BD → acceso directo | Datos cifrados con AES → inútiles sin clave |

## CUÁNDO usar cada tipo

| Escenario | Tipo | Herramienta .NET | Ejemplo |
|-----------|------|------------------|---------|
| Almacenar passwords | Hash | `BCrypt.Net` | Login de usuarios |
| Datos sensibles en BD | Symmetric | `System.Security.Cryptography` (AES) | Números de tarjeta |
| Comunicación cliente-servidor | TLS | Certificados SSL/TLS | HTTPS |
| Intercambio de claves | Asymmetric | RSA, ECC | JWT con RS256 |
| Secrets en CI/CD | Symmetric | Vault, GitHub Secrets | Connection strings |
| Firma de documentos | Asymmetric | RSA, ECDSA | Contratos digitales |

## CÓMO implementar en .NET

### Hash de passwords (bcrypt)

```bash
dotnet add package BCrypt.Net-Next
```

```csharp
// Hash al registrar usuario
string hashedPassword = BCrypt.Net.BCrypt.HashPassword(plainPassword);

// Verificar al hacer login
bool isValid = BCrypt.Net.BCrypt.Verify(plainPassword, hashedPassword);
```

> **NUNCA uses** MD5 o SHA1 para passwords. Son rápidos demais y tienen colisiones conocidas. Usa **bcrypt** o **argon2**.

### Encriptación simétrica (AES)

```csharp
using System.Security.Cryptography;

// Cifrar
using var aes = Aes.Create();
aes.Key = keyFromConfig;  // 256 bits para AES-256
aes.IV = iv;              // Initialization Vector

using var encryptor = aes.CreateEncryptor();
byte[] encrypted = encryptor.TransformFinalBlock(plainBytes, 0, plainBytes.Length);

// Descifrar
using var decryptor = aes.CreateDecryptor();
byte[] decrypted = decryptor.TransformFinalBlock(encrypted, 0, encrypted.Length);
```

### HTTPS en .NET

```csharp
// Program.cs — Redirigir HTTP a HTTPS
app.UseHttpsRedirection();

// En Producción: certificado válido (Let's Encrypt, Azure, etc.)
// En Desarrollo: certificado de desarrollo de .NET
```

## DÓNDE aplicar

| Capa | Acción | Prioridad |
|------|--------|-----------|
| **Tránsito** | Siempre HTTPS (nunca HTTP en producción) | 🔴 Crítica |
| **Passwords** | Hash con bcrypt (nunca MD5/SHA1) | 🔴 Crítica |
| **Reposo** | Encriptar campos sensibles en BD | 🟠 Alta |
| **Secrets** | User Secrets (dev), Vault (prod) | 🟠 Alta |
| **Logs** | Nunca loguear datos sensibles | 🟡 Media |

## Errores comunes

| Error | Causa | Solución |
|-------|-------|----------|
| Password filtrado y descifrado | Usado MD5/SHA1 | Migrar a bcrypt |
| Datos leídos sin autorización | Sin encriptación en reposo | Usar AES-256 |
| MITM (Man-in-the-Middle) | HTTP sin TLS | Habilitar HTTPS |
| Secret en el repo | Hardcoded en appsettings.json | Mover a User Secrets/env vars |

## Referencia
- [[00-Index]]
- [[Seguridad/Autenticación-y-Autorización]]
- [[Patrones/Anti-Patrones-Seguridad]]

---
#seguridad #encryption #hashing #tls #aes #owasp #bcrypt
