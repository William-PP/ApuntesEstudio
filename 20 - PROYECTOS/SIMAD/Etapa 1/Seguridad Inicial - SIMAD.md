---
type: documento
state: activa
priority: alta
created: 2026-09-05
updated: 2026-09-05
related: [SIMAD]
tags: [simad, seguridad, clasificacion-datos, ley-1581, secretos, amenazas]
aliases: [Seguridad Inicial SIMAD]
---

# Seguridad Inicial — SIMAD

> [!info] Etapa 1 — Definición y Planificación Inicial
> Postura de seguridad inicial del **Sistema Integral de Gestión Deportiva (SIMAD)**: clasificación de datos, regulaciones aplicables, paisaje de amenazas y política de secretos. Hace parte del entregable de la **Etapa 1** según la [[Guía - Documentación de Proyectos de Software]].

---

## Clasificación de Datos

| Tipo de Dato | Ejemplos | ¿Es Sensible? |
|---|---|---|
| Datos personales | Nombres, documentos, fechas de nacimiento, etc | Sí |
| Datos de usuario | Correos electrónicos, contraseñas, credenciales | Muy sensible |
| Datos médicos | Registros médicos, incapacidades, lesiones, resultados de análisis | Alta |
| Datos operativos | Fechas de eventos, entrenamientos, resultados de eventos, reportes diarios | No |
| Datos institucionales | Reportes de bienestar | — |

---

## Regulaciones Aplicables

| Normativa | Descripción | Aplicación |
|---|---|---|
| **Ley 1581 de 2012** | Régimen General de Protección de Datos Personales. Desarrolla derechos constitucionales de acceso, rectificación y actualización de datos en bases de datos. | Toda entidad pública o privada que procese datos personales en bases de datos. |
| **Decreto 1377 de 2013** | Reglamentación de la Ley 1581. Establece el principio de seguridad: obligación de adoptar medidas técnicas, humanas y administrativas para proteger los datos. | Responsables y encargados de bases de datos, incluyendo aplicaciones web. |
| **Resolución 1995 de 1999** | Establece que la historia clínica es un documento privado y sometido a reserva y regula aspectos de acceso, custodia y confidencialidad. | Cuando el sistema maneja historias clínicas o registros médicos. |
| **PLE C274/2025** *(en trámite)* | Proyecto de Ley Estatutaria que actualiza la Ley 1581: amplía la aplicación extraterritorial, eleva sanciones hasta el 5% de los ingresos anuales e introduce obligaciones en IA y ciberseguridad. | Organizaciones públicas y privadas, incluyendo empresas extranjeras sin sede en Colombia que ofrezcan servicios a ciudadanos colombianos. |
| **Constitución Art. 15** | Derecho fundamental a la intimidad personal y familiar, al buen nombre y al derecho de toda persona a conocer, actualizar y rectificar su información. | Todos los ciudadanos y su información personal. |

---

## Paisaje de Amenazas

| Amenaza | ¿Qué Busca? | Ejemplo |
|---|---|---|
| **Ciberdelincuentes** | Robar información | Robar credenciales o información para cometer fraude |
| **Usuarios internos comprometidos** *(Phishing)* | Acceso al sistema | Cuenta legítima que es robada y comprometida por medio de Phishing |
| **Atacantes oportunistas** *(DDoS)* | Vulnerabilidades | Intentar explotar una API mal configurada enviando miles de solicitudes instantáneas. |
| **Usuarios internos maliciosos** *(Acceso no autorizado)* | Empleados o usuarios autorizados que utilizan indebidamente sus permisos | Consultar información de personas que no deberían poder visualizar. |
| **Inyección SQL** | Manipulación de consultas para acceder o modificar información de la base de datos sin autorización | Introduce código SQL malicioso en un formulario para consultar datos de usuarios. |
| **Exposición de datos médicos** | Acceso, divulgación o filtración de información relacionada con la salud de los usuarios. | Un usuario sin permisos puede consultar el historial médico de un menor. |
| **Insider Threat** | Uso indebido de los privilegios de un usuario interno para acceder, modificar o divulgar información. | Un administrador consulta información médica que no necesita para realizar su trabajo. |
| **Fuerza bruta** | Intentos repetidos y automatizados para descubrir las credenciales de un usuario. | Un atacante prueba miles de combinaciones de contraseñas para acceder a una cuenta. |
| **Errores de configuración** | Configuraciones incorrectas que generan vulnerabilidades o exponen recursos del sistema. | Una base de datos queda accesible desde Internet sin las restricciones adecuadas. |
| **Robo de credenciales** | Obtención y utilización ilegítima de nombres de usuario, contraseñas o tokens de acceso. | Un atacante obtiene el token de sesión de un administrador y entra al sistema. |
| **MITM** *(Man-in-the-Middle)* | Interceptación de la comunicación entre un usuario y el sistema. | Un atacante intercepta información transmitida cuando un usuario utiliza una red Wi-Fi pública insegura. |

---

## Políticas de Secretos

| Tipo de Secreto | Almacenamiento | Rotación | Acceso |
|---|---|---|---|
| **Contraseñas Base de Datos** | Vault | Cada 90 días | Solo Líder Técnico, sistema automático |
| **API Keys Externas** | Vault o Variables de entorno encriptadas | Cada 180 días | Solo backend, nunca en frontend |
| **Claves de Encriptación** | Vault, nunca en código | Cada 365 días *(política, no operativo)* | Rotación automática con versiones |
| **Tokens JWT/Sesión** | Vault, en memoria servidor | Cada 30 días | Solo servidor, cliente recibe token firmado |
| **Certificados SSL/TLS** | Vault, servidor web | Antes de expirar *(Let's Encrypt automático)* | Solo servidor web |
| **Credenciales OAuth** | Vault | Cada 180 días | Solo backend |
| **Secrets en Git** | NUNCA. Usar .env local + .gitignore | N/A | Desarrolladores usan .env local |
| **Contraseña Admin Sistema** | Vault + contraseña fuerte (16+ caracteres) | Cada 90 días | Líder Técnico solo *(2FA activado)* |
| **Tokens Terceros** *(Notificaciones)* | Vault, nunca hardcoded | Cada 180 días | Solo backend |
| **Backup Encryption Key** | Vault separado, offline | Cada 365 días | Solo para recuperación de desastres |
| **Contraseñas de Usuarios** *(almacenadas)* | BD encriptada con AES-256 | N/A *(usuario controla)* | Solo usuario puede cambiar |
| **Variables de Configuración Sensibles** | Variables de entorno + Vault | Cada 90 días | Solo en tiempo deploy |

---

## Resumen de Recomendaciones

- Implementar **Vault** como solución centralizada de gestión de secretos
- Usar **variables de entorno** encriptadas para configuraciones sensibles
- Nunca commitear secretos en Git (usar `.gitignore`)
- Mantener rotación de credenciales según política definida
- Limitar acceso a secretos por rol y necesidad operativa
- Activar **2FA** para cuentas administrativas
- Encriptar contraseñas de usuarios con **AES-256**
- Cumplir con regulaciones colombianas (Ley 1581, Decreto 1377, etc.)

---

## Referencia

- [[SIMAD]] — epicentro del proyecto
- [[Acta de Constitución - SIMAD]] — objetivos de seguridad y cumplimiento del proyecto (Etapa 1)
- [[Stack Tecnológico - SIMAD]] — herramientas que implementan esta postura (Etapa 1)
- [[Guía - Documentación de Proyectos de Software]] — plantilla de la Etapa 1 aplicada

#simad #seguridad #clasificacion-datos #ley-1581 #secretos #amenazas #etapa-1