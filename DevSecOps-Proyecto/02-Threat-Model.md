---
tags: [proyecto/gestion-deportiva, devsecops, threat-model, seguridad]
created: 2026-07-14
updated: 2026-07-18
---

# Threat Model

## ¿Qué es Threat Modeling?

El modelado de amenazas (threat modeling) es un proceso estructurado, repetible y proactivo que se utiliza para obtener información procesable sobre las características de seguridad de un sistema. Su objetivo principal es analizar representaciones de un sistema para resaltar preocupaciones sobre la seguridad y la privacidad.

> [!info] Las cuatro preguntas fundamentales
> El proceso de modelado de amenazas busca responder de manera sistemática a cuatro preguntas clave:
> 1. **¿En qué estamos trabajando?** — Se define el alcance y se modela el sistema (por ejemplo, mediante diagramas de flujo de datos).
> 2. **¿Qué puede salir mal?** — Se identifican las posibles amenazas y qué atributos de seguridad podrían verse violados.
> 3. **¿Qué vamos a hacer al respecto?** — Se determinan las respuestas y mitigaciones para cada amenaza identificada.
> 4. **¿Hicimos un trabajo lo suficientemente bueno?** — Se realiza una revisión y validación de todo el modelo y las medidas adoptadas.

### Fases del proceso

Aunque no existe un estándar industrial único, la mayoría de los enfoques incluyen estos pasos:

1. **Modelado del sistema** — Se utilizan Diagramas de Flujo de Datos (DFDs) para visualizar las interacciones, almacenes de datos y límites de confianza. También puede emplearse el brainstorming para identificar procesos clave.
2. **Identificación de amenazas** — Se utilizan técnicas como STRIDE (Spoofing, Tampering, Repudiation, Information Disclosure, Denial of Service, Elevation of Privileges) para categorizar lo que puede salir mal.
3. **Respuesta y mitigación** — Para cada amenaza, se decide si se va a mitigar (reducir la probabilidad), eliminar (quitar la función causante), transferir (pasar la responsabilidad) o aceptar (asumir el riesgo).
4. **Validación** — Una revisión final por parte de los interesados para asegurar que el modelo es preciso y las mitigaciones son comprobables.

### Ventajas de su aplicación

- **Identificación temprana de riesgos** — Permite que la seguridad se "integre" en el diseño en lugar de añadirse después de que el sistema esté en producción.
- **Aumento de la conciencia de seguridad** — Obliga a los equipos a "pensar como un atacante" y comprender mejor el panorama de amenazas.
- **Visibilidad mejorada** — Proporciona una comprensión profunda de los flujos de datos y los límites de confianza de la aplicación.

> [!tip] Manifiesto del Modelado de Amenazas
> Se debe valorar más una cultura de encontrar y solucionar problemas de diseño que el simple cumplimiento de requisitos. Es un proceso que debe realizarse de forma continua y refinarse a medida que el sistema evoluciona, integrándose idealmente en el ciclo de vida de desarrollo de software (SDLC).

---

## Datos que Maneja la Aplicación

> Basado en el [[DevSecOps-Proyecto/GestionDeportiva-Diccionario-Datos]] §13.

| Dato                                               | ¿Sensible?                     | Requiere cifrado en tránsito | Requiere cifrado en reposo   | Categoría OWASP                         |
| -------------------------------------------------- | ------------------------------ | ---------------------------- | ---------------------------- | --------------------------------------- |
| `numero_documento` (cédula)                        | **Sí**                         | Sí (HTTPS)                   | Sí (column-level encryption) | A04 Cryptographic Failures              |
| `email`                                            | **Sí**                         | Sí                           | No (pero enmascarar en logs) | A04 Cryptographic Failures              |
| `direccion`, `telefono`, `fecha_nacimiento`        | **Sí**                         | Sí                           | Sí (Ley 1581)                | A04 Cryptographic Failures              |
| `tipo_sangre`, `lesiones` (Deportista)             | **Sí**                         | Sí                           | Sí (dato de salud)           | A04 Cryptographic Failures              |
| Credenciales de usuario (login)                    | **Sí**                         | Sí                           | Sí (hash bcrypt)             | A04 Cryptographic Failures              |
| `Resultado.medalla`                                | No (pero íntegro)              | Sí                           | No                           | A08 Software or Data Integrity Failures |
| Torneos, categorías, modalidades                   | No                             | Sí                           | No                           | —                                       |
| Inscripciones                                      | No                             | Sí                           | No                           | —                                       |
| Auditoría (`Auditoria.valor_nuevo`, `id_registro`) | **Sí** (contiene PII en claro) | Sí                           | Sí                           | A04 Cryptographic Failures              |

---

## Análisis STRIDE

### S — Spoofing (Suplantación de identidad)
> *¿Quién puede hacerse pasar por otro usuario?*

| #   | Escenario                                                                    | Riesgo                                                | Mitigación                                                               | OWASP                       | Estado    |
| --- | ---------------------------------------------------------------------------- | ----------------------------------------------------- | ------------------------------------------------------------------------ | --------------------------- | --------- |
| 1   | Alguien usa credenciales robadas para autenticarse como otro usuario         | Acceso no autorizado a datos personales y operaciones | JWT con secret seguro; rotación de tokens; rate limiting en login        | A07 Authentication Failures | Pendiente |
| 2   | Un atacante intercepta un token JWT en tránsito (MITM)                       | Suplantación de sesión                                | HTTPS obligatorio; tokens con expiración corta                           | A04 Cryptographic Failures  | Pendiente |
| 3   | Alguien intenta registrar un email ya existente para tomar control de cuenta | Account takeover parcial                              | Validación de email único; verificación de email antes de activar cuenta | A07 Authentication Failures | Pendiente |

### T — Tampering (Manipulación de datos)
> *¿Puede alguien modificar datos que no debiera?*

| # | Escenario | Riesgo | Mitigación | OWASP | Estado |
|---|-----------|--------|------------|-------|--------|
| 1 | Un usuario modifica su propio `id_deportista` en un request para inscribir a otro | Inscripción fraudulenta | El ID del usuario debe venir del token JWT, nunca del body del request | A01 Broken Access Control | Pendiente |
| 2 | Un atacante modifica `Resultado.medalla` vía API directamente | Adulteración de resultados de competencia | Audit log + restricción de rol (solo Organizador/Admin puede escribir en Resultado) | A08 Software or Data Integrity Failures | Pendiente |
| 3 | Manipulación de datos en Auditoría (si un atacante obtiene acceso a la tabla) | Destrucción de evidencia de cambios | `Auditoria` debe ser append-only (solo INSERT, nunca UPDATE/DELETE) | A08 Software or Data Integrity Failures | Pendiente |

### R — Repudiation (Repudio)
> *¿Puedo rastrear quién hizo qué?*

| # | Escenario | Riesgo | Mitigación | OWASP | Estado |
|---|-----------|--------|------------|-------|--------|
| 1 | Un admin registra un resultado y después niega haberlo hecho | Sin evidencia de quién modificó el resultado | Audit log con `usuario_db` y `fecha_operacion` en cada cambio; logging estructurado en la API | A09 Logging and Alerting Failures | Pendiente |
| 2 | Un atacante borra evidencia de un cambio no autorizado | Imposible rastrear el incidente | `Auditoria` append-only; logs centralizados con retención definida; alerts si se detecta acceso inusual a la tabla | A09 Logging and Alerting Failures | Pendiente |
| 3 | `tr_AuditoriaPersona` solo cubre INSERT/DELETE, no UPDATE | Un UPDATE de `direccion`, `telefono` o `email` no queda auditado | Agregar auditoría de UPDATE sobre campos sensibles de Persona | A09 Logging and Alerting Failures | Pendiente |

### I — Information Disclosure (Divulgación de información)
> *¿Pueden leer datos sensibles?*

| # | Escenario | Riesgo | Mitigación | OWASP | Estado |
|---|-----------|--------|------------|-------|--------|
| 1 | `numero_documento` (cédula) se expone crudo en respuestas de API | Fuga de dato personal que se propaga como FK a todo el modelo | Nunca devolver `numero_documento` en respuestas; usar IDs internos (`id_inscripcion`, `id_entrenador_deportista`) como referencia pública | A04 Cryptographic Failures | Pendiente |
| 2 | `tr_AuditoriaPersona` escribe `email` y `numero_documento` sin enmascaramiento en `Auditoria` | PII en claro visible para cualquier rol con acceso de lectura a la tabla | Corregir el trigger para hashear o truncar esos valores antes de insertar | A04 Cryptographic Failures | Pendiente |
| 3 | Stack traces expuestos al cliente en errores 500 | Revela arquitectura interna, versiones, rutas | Manejo de excepciones global; respuesta genérica al cliente; log completo solo en servidor | A02 Security Misconfiguration | Pendiente |
| 4 | Logs contienen datos sensibles (`email`, `telefono`) en claro | Fuga vía sistemas de monitoreo o backups de logs | Enmascarar campos sensibles antes de loguear; estructurar logs para facilitar el filtrado | A04 Cryptographic Failures | Pendiente |

### D — Denial of Service (Denegación de servicio)
> *¿Pueden tumbar la aplicación?*

| # | Escenario | Riesgo | Mitigación | OWASP | Estado |
|---|-----------|--------|------------|-------|--------|
| 1 | Un atacante envía miles de requests de inscripción al mismo endpoint | API saturada, usuarios legítimos no pueden inscribirse | Rate limiting por IP y por usuario en endpoints de escritura | A01 Broken Access Control | Pendiente |
| 2 | Consultas pesadas sin paginación (ej. listar todas las inscripciones de un torneo grande) | Timeout de BD, consumo de memoria excesivo | Paginación obligatoria en endpoints de listado; timeouts configurados | A02 Security Misconfiguration | Pendiente |

### E — Elevation of Privilege (Elevación de privilegios)
> *¿Pueden hacer más de lo que deben?*

| # | Escenario | Riesgo | Mitigación | OWASP | Estado |
|---|-----------|--------|------------|-------|--------|
| 1 | Un Deportista intenta registrar un resultado (función de Organizador) | Modificación no autorizada de resultados | Autorización por rol en cada endpoint: `[Authorize(Roles = "Organizador")]` para escritura en Resultado | A01 Broken Access Control | Pendiente |
| 2 | Un usuario normal intenta modificar el estado de un Torneo | Control no autorizado del ciclo de vida de torneos | `sp_ActualizarEstadoTorneo` debe validarse en la capa de API, no solo en la BD | A01 Broken Access Control | Pendiente |
| 3 | Un atacante inyecta SQL vía un campo de búsqueda | Acceso o modificación no autorizada de datos | EF Core con parameterized queries; FluentValidation en todos los inputs | A05 Injection | Pendiente |

---

## Superficie de Ataque

### Endpoints expuestos

> [!warning] Incompleto
> Esta sección se completa cuando se diseñe la API. Ver [[DevSecOps-Proyecto/GestionDeportiva-Diccionario-Datos]] §13 para los endpoints identificados.

| Método | Ruta | Autenticado? | Rate Limited? | Rol requerido |
|--------|------|-------------|---------------|---------------|
| — | — | — | — | — |

### Datos en tránsito
- Protocolo: HTTPS (TLS 1.2+)
- HTTP redirect a HTTPS: Sí

### Datos en reposo
- BD encryptada: Pendiente (column-level encryption para campos sensibles)
- Secrets en: User Secrets (desarrollo) / Environment Variables (producción)

### Autenticación
- Mecanismo: JWT (JSON Web Tokens)
- Token expiry: 60 minutos (pendiente de definir)
- Refresh tokens: Pendiente

---

## Matriz de Riesgos

| # | Amenaza | Probabilidad | Impacto | Riesgo | Mitigación |
|---|---------|-------------|---------|--------|------------|
| 1 | Fuga de `numero_documento` vía API | Alta | Alto | **Crítico** | Nunca exponerlo en respuestas; cifrado en reposo |
| 2 | Adulteración de `Resultado.medalla` | Media | Alto | **Alto** | Audit log + restricción de rol |
| 3 | PII en texto plano en `Auditoria` | Alta | Medio | **Alto** | Corregir trigger; enmascarar PII |
| 4 | Brute force en login | Alta | Medio | **Alto** | Rate limiting + account lockout |
| 5 | SQL Injection | Baja | Crítico | **Alto** | EF Core parameterized queries + FluentValidation |
| 6 | Inscripciones duplicadas (bug de NULL) | Alta | Bajo | **Medio** | Corregir `UQ_Inscripcion_Unica` |
| 7 | Stack traces expuestos | Media | Medio | **Medio** | Manejo de excepciones global |
| 8 | DoS vía endpoints sin rate limiting | Media | Medio | **Medio** | Rate limiting en todos los endpoints de escritura |

---

## Plan de Mitigación

### Prioridad Alta (antes de Fase 0)
1. Corregir `UQ_Inscripcion_Unica` — quitar `id_Entrenador` del índice único (bug confirmado)
2. Nunca exponer `numero_documento` crudo en respuestas de API
3. Configurar HTTPS obligatorio

### Prioridad Media (durante Fase 0)
4. Implementar JWT con expiración corta
5. Rate limiting en endpoints de escritura
6. Manejo de excepciones global (sin stack traces al cliente)
7. Corregir `tr_AuditoriaPersona` — enmascarar PII

### Prioridad Baja (durante Fase 1-3)
8. Auditoría de UPDATE sobre campos sensibles de Persona
9. Paginación en endpoints de listado
10. Validación cruzada TorneoModalidad ↔ TorneoCategoria

---

## Referencia

- [[DevSecOps-Proyecto/00-Visión-General]]
- [[DevSecOps-Proyecto/01-Arquitectura]]
- [[DevSecOps-Proyecto/03-Decisiones-Técnicas]]
- [[DevSecOps-Proyecto/GestionDeportiva-Diccionario-Datos]] —§13 (campos sensibles), §14 (auditoría), §15 (discrepancias DDL)
- [[DevSecOps-Proyecto/GestionDeportiva-Diagrama-Clases]] —§ Notas de diseño (gaps de integridad)
- [[DevSecOps-Estudio/Patrones/Best-Practices]] — checklist de mitigaciones
- [[DevSecOps-Estudio/Patrones/Anti-Patrones-Seguridad]] — anti-patrones a evitar
- [[DevSecOps-Estudio/Seguridad/Bases-Datos-Vulnerabilidades]] — CVSS, CWE, CVE

---
#threat-model #stride #seguridad #proyecto #gestion-deportiva #devsecops
