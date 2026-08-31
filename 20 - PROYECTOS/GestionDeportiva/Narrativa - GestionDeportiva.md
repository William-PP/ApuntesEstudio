---
type: documento
state: activa
priority: alta
created: 2026-08-30
updated: 2026-08-30
related: [GestionDeportiva, SIMAD-ITM]
tags: [gestion-deportiva, narrativa, requisitos, itm, proyecto]
aliases: [Narrativa GestionDeportiva, Requisitos Sistema Deportivo ITM]
---

# Narrativa — Sistema de Gestión Deportiva ITM

> [!info] Contexto
> Narrativa verbal del **módulo deportivo** del sistema integral SIMAD-ITM. Documento compartido con el proyecto hermano [[20 - PROYECTOS/SIMAD-ITM/SIMAD-ITM]].

---

## 1. Descripción del Problema Actual

El Instituto Tecnológico Metropolitano de Medellín enfrenta un desafío crítico en la gestión integral de su programa de entrenamiento deportivo. Actualmente, no se cuenta con un sistema centralizado para recopilar, organizar y acceder a la información de los deportistas de manera eficiente. La información sobre competencias, lesiones, restricciones médicas, desempeño técnico y estado general de bienestar se encuentra dispersa en múltiples fuentes: algunos datos se encuentran en hojas de cálculo desactualizadas, otros en registros de papel, y gran parte solo existe en la memoria de los entrenadores. Esta fragmentación impide una visión holística del deportista y dificulta la toma de decisiones informadas sobre su preparación y participación en competiciones.

---

## 2. Causas Identificadas

- **Ausencia de información centralizada:** Los datos de los deportistas no tienen un repositorio único. Cuando un entrenador necesita saber si un deportista tiene restricciones activas, debe contactar directamente con el atleta, revisar correos antiguos, o buscar en documentos físicos dispersos. Esto consume tiempo valioso.
- **Ausencia en procesos críticos:** El registro de competencias, la recopilación de datos de bienestar, y el seguimiento de restricciones médicas se hacen completamente de forma manual o no se hacen. Un entrenador podría perder información importante simplemente porque no tuvo tiempo de documentarse inmediatamente después de una sesión.
- **Comunicación unidireccional e incompleta:** Los atletas no tienen un canal oficial para auto-reportar su estado (fatiga, dolor muscular, molestias). Si sufren una lesión o una molestia, deben esperar a la próxima sesión de entrenamiento para comunicarlo. Mientras tanto, podrían estar entrenando bajo condiciones que empeoren su estado.
- **Ausencia de historial y contexto temporal:** Sin registros históricos centralizados, resulta imposible identificar patrones en el desempeño de un atleta. ¿Siempre tiene el mismo tipo de molestia después de cierto tipo de entrenamiento? ¿Mejora su desempeño después de descansos específicos? Estas preguntas no pueden responderse sin datos consolidados con *timestamps* exactos.
- **Redundancia y pérdida de información en transiciones:** Cuando un entrenador se jubila o cambia de deporte asignado, el conocimiento acumulado sobre los atletas no se transfiere de manera estructurada. El nuevo entrenador debe empezar casi desde cero, perdiendo meses de observaciones y contexto.

---

## 3. Impacto en el Negocio y los Usuarios

- **Para los entrenadores y monitores:** El impacto es directo: gastan grandes cantidades de tiempo buscando información que debería estar centralizada. Su capacidad para dirigir un entrenamiento se puede ver afectada por tareas administrativas. Además, no pueden hacer seguimiento riguroso del progreso técnico porque no tienen un registro estructurado de observaciones.
- **Para los deportistas:** El impacto es aún más crítico. Sin un sistema para auto-reportar su estado, sufren entrenamientos que pueden agravar lesiones leves. No ven su progreso visualizado ni reciben retroalimentación estructurada. Existe mayor riesgo de lesiones severas porque las restricciones médicas no están siempre visibles al entrenador.
- **Para la institución:** El impacto es de eficiencia operativa y responsabilidad. Sin un sistema claro, es difícil seguir el proceso de los diferentes equipos. Si un atleta sufre una lesión agravada, la institución podría enfrentar reclamaciones sin evidencia de que fueron advertidos o que su restricción fue comunicada. Además, el instituto pierde visibilidad sobre cuáles atletas están realmente listos para competir formalmente.

---

## 4. Flujo Actual de Procesos

### Escenario 1: Registrar una Competencia
Un entrenador lleva un equipo a una competencia. Después del evento, debe llevar un registro manual de quién participó, en qué modalidad, en qué categoría, y cuáles fueron los resultados. Este registro se escribe en papel o en un archivo de Word. Cuando regresa a ITM, podría tardar días en pasar toda esa información a un archivo compartido, y para entonces, ya ha perdido detalles (tiempo exacto, observaciones técnicas). Si alguien más necesita consultar esos resultados la próxima semana, tiene que contactar al entrenador.

### Escenario 2: Auto-reportar Bienestar
Un atleta entrena el lunes. El miércoles siente dolor muscular que no es normal. Espera hasta la próxima sesión de entrenamiento del viernes para decirle al entrenador. Para entonces, han pasado alrededor de 5 días y el entrenador no sabía que debía darle un entrenamiento menos intenso el jueves. El atleta entrena duro el jueves, empeorando la molestia. No hay registro de cuándo comenzó el problema ni de su evolución.

### Escenario 3: Crear una Restricción Médica
Un atleta se lesiona en un entrenamiento. El entrenador lo deriva al médico, quien identifica un esguince de tobillo y emite una incapacidad médica de 2 semanas. El médico entrega la incapacidad al atleta. El atleta se la lleva a casa. Tres días después, hay una competencia programada. El entrenador no tiene acceso a toda la información del atleta, esa información está en la incapacidad que el deportista tiene en su casa. El entrenador asume que el atleta está disponible y lo inscribe en la competencia.

### Escenario 4: Transición de Entrenador
Un entrenador principal de atletismo se jubila. Tiene 12 atletas, y cada uno tiene un archivo en su computadora personal con observaciones técnicas de 3 años. Las observaciones nunca fueron consolidadas en un lugar accesible. El nuevo entrenador llega y debe empezar de cero. Pierde todo el contexto sobre debilidades técnicas, historial de lesiones, y patrones de desempeño de cada atleta.

---

## 5. Propuesta de Solución

La solución es un **Sistema de Gestión Deportiva** centralizado y accesible construido sobre una arquitectura de base de datos robusta que captura la complejidad real del entrenamiento deportivo. El sistema está dividido en cinco niveles de funcionalidad:

1. **Nivel 1 — Identidad y Roles:** El sistema mantiene un registro maestro de todas las personas en ITM (`Persona`), diferenciando entre Entrenadores, Deportistas, y Administradores. Cada persona tiene un perfil único con información de contacto, documento de identidad y fotografía. Esto asegura que la información nunca se duplica y que siempre hay una fuente de verdad sobre quién es quién.
2. **Nivel 2 — Acceso y Permisos:** Mediante una tabla de Roles y Permisos, el sistema permite que diferentes usuarios vean diferentes vistas de los datos. Un atleta solo ve su propia información y sus restricciones activas. Un entrenador ve a todos sus atletas y sus historiales. Un administrador ve todo. Esto protege la privacidad y garantiza que cada usuario solo acceda a lo que necesita.
3. **Nivel 3 — Estructura Deportiva:** El sistema modela Equipos (grupos de atletas bajo un entrenador) y sus relaciones. Permite que el sistema sepa automáticamente cuál entrenador es responsable de cuál atleta, y si ese atleta ha sido transferido a otro equipo, mantiene el historial completo.
4. **Nivel 4 — Eventos y Competencias:** Las Competencias (Torneos) ahora son entidades de primer orden. Cada torneo tiene modalidades (atletismo, natación, etc.) y Categorías (edad, peso). Cada Inscripción registra automáticamente quién participó, en qué, cuándo. Los resultados se capturan con tiempo exacto, posición y observaciones técnicas. El sistema impide inscribir a un atleta con restricciones activas.
5. **Nivel 5 — Gestión Continua:** Este es el corazón de la innovación. Los Atletas pueden auto-reportar su Bienestar semanalmente mediante un formulario simple (escala de fatiga, dolor, estrés, sueño entre otras opciones). Los deportistas y/o entrenadores pueden registrar Novedades Médicas (lesiones, restricciones, alergias). El sistema calcula automáticamente cuáles restricciones están activas (no han vencido) y las muestra en rojo cuando un entrenador intenta asignar un entrenamiento de impacto. Los Entrenadores documentan Rutinas Técnicas (planes de entrenamiento con fechas y estado) que quedan en el sistema permanentemente. Todo esto se audita mediante *Timestamps* y una Tabla de Auditoría que registra quién hizo qué cambio y cuándo.

### Beneficios Operacionales:
- **Para Entrenadores:** Acceso instantáneo al estado completo de cualquier atleta. Visibilidad automática de restricciones. Historial completo de observaciones técnicas guardadas en un solo lugar. Transiciones sin pérdida de información cuando otro entrenador toma el cargo.
- **Para Atletas:** Un canal seguro para reportar su estado sin esperar a la próxima sesión. Visibilidad de su progreso y restricciones activas. Profesionalismo en el seguimiento.
- **Para la Institución:** Auditoría completa de todos los procesos. Evidencia clara de que se siguieron protocolos de seguridad. Capacidad de analizar tendencias (¿qué tipo de atleta sufre más lesiones? ¿Cuál modalidad tiene mejor desempeño?). Transferencia de conocimiento sin fricción.

> [!note] Principio de diseño
> El sistema reconoce que los datos de competencias, lesiones, entrenamientos y bienestar son dinámicos y requieren captura en tiempo real. Por eso todo está *timestamped*, versionado, y auditable. Ningún dato se pierde, ninguna decisión se toma sin contexto, y ningún cambio de entrenador resulta en pérdida de memoria institucional.

---

## 6. Requisitos de Usuario

| ID. Requisito | Nombre del Requisito | Descripción del Requisito |
| :--- | :--- | :--- |
| **RU-001** | Deportista | Usuario que participa en entrenamientos y competencias. Debe poder auto-reportar su bienestar, historial médico, registrar asistencias, solicitar unirse a equipos y ver sus resultados en competencias. |
| **RU-002** | Entrenador | Usuario responsable de un equipo de deportistas. Debe poder registrar competencias, ver información completa de sus atletas (bienestar, historial médico, asistencias, resultados), crear rutinas técnicas y gestionar asignaciones de deportistas. |
| **RU-003** | Monitor | Usuario con permisos limitados para supervisar un equipo. Puede ver inscripciones, asistencias, resultados de su equipo y crear torneos. No puede ver historial médico ni crear rutinas. |
| **RU-004** | Administrador | Usuario técnico con acceso root al sistema. Gestiona usuarios, roles, permisos, configuración global, backups, auditoría completa del sistema. |

---

## 7. Requisitos de Sistema – Funcionales

| ID Requisito | Nombre del Requisito | Descripción del Requisito | Usuario |
| :--- | :--- | :--- | :--- |
| **RF-001** | Crear Torneo | El usuario (Entrenador, Monitor, Admin) puede crear un torneo. | Entrenador, Monitor, Admin |
| **RF-002** | Configurar Modalidades del Torneo | El usuario puede asignar una o más Modalidades (atletismo, natación, etc.) al torneo mediante tabla TorneoModalidad. Cada modalidad puede activarse/desactivarse. | Entrenador, Monitor, Admin |
| **RF-003** | Configurar Categorías del Torneo | El usuario puede asignar una o más Categorías (edad 18-25, peso 60-80kg, etc.) al torneo mediante tabla TorneoCategoria. Cada categoría puede activarse/desactivarse. | Entrenador, Monitor, Admin |
| **RF-004** | Registrar Inscripción (Entrenador/Monitor) | Entrenador o Monitor inscribe deportista a torneo y notifica al Deportista vía email/WhatsApp. | Entrenador, Monitor |
| **RF-005** | Registrar Inscripción (Auto-inscripción) | Deportista se auto-inscribe a torneo. | Deportista |
| **RF-006** | Validar Restricción Médica en Inscripción | El sistema consulta Novedad_Medica si se tiene una restriccion y no tiene aval el sistema no le permite inscribirse de caso contrario si tiene un aval puede competir. | Sistema (automático) |
| **RF-007** | Registrar Resultado y observaciones Post-Competencia | Entrenador, Monitor, Admin registran resultados y observaciones | Entrenador, Monitor, Admin |
| **RF-008** | Registrar observaciones Post-Competencia | Deportista registra sus propias observaciones | Deportista |
| **RF-009** | Visualizar Resultados de Competencia | Sistema muestra tabla con: Deportista, Modalidad, Categoría, Tiempo, Posición, Medalla Disponible inmediatamente post-registro. | Entrenador, Monitor, Deportista (solo sus resultados), Admin |
| **RF-010** | Crear Formulario Semanal de Bienestar | Deportista completa el formulario diario. Campos obligatorios: Escala 1-10 (Sueño, Fatiga, Dolor_muscular, Estrés), ¿Molestias? Si Molestias=SI: Lugar, ¿Incapacita? Campos opcionales: Peso, Observaciones, Archivo foto/documento. Timestamp automático. | Deportista |
| **RF-011** | Notificar Recordatorio de Bienestar (Diario) | Sistema envía notificación diaria vía email/WhatsApp: "Completa tu reporte de bienestar de hoy." Link directo al formulario | Sistema (automático) |
| **RF-012** | Visualizar Historial de Bienestar | Deportista ve su historial completo. Entrenador ve a sus atletas. Admin ve todo. | Deportista, Entrenador, Admin |
| **RF-013** | Registrar Novedad Médica | El deportista reporta si tiene una lesión, enfermedad o alergia, agregando detalles y una foto opcional del comprobante. | Deportista |
| **RF-014** | Confirmar/Rechazar Novedad | El entrenador revisa los reportes médicos pendientes y decide si los aprueba, los rechaza o pide más pruebas. | Entrenador |
| **RF-015** | Registrar Restricción Médica Formal | El entrenador registra directamente una limitación física (como "sin impacto") con su fecha estimada de recuperación. | Entrenador, Admin |
| **RF-016** | Bloquear Inscripción por Restricción | El sistema impide que un deportista lesionado se inscriba a un torneo, a menos que presente aval médico y permiso del entrenador. | Sistema (automático) |
| **RF-017** | Levantar Restricción Médica | El entrenador quita la restricción cuando el deportista se recupera, sube su alta médica o no siente dolor por 2 semanas. | Entrenador |
| **RF-018** | Visualizar Restricciones Activas | El entrenador ve en rojo a los lesionados y el sistema le muestra una alerta si intenta asignarles ejercicios de alto impacto. | Entrenador |
| **RF-019** | Reasignar Deportistas a Nuevo Entrenador | El administrador cambia a los deportistas de entrenador sin borrar ningún dato de su historial. | Admin |
| **RF-020** | Visualizar Historial Post-Transición | El nuevo entrenador puede consultar de inmediato todo el historial previo del deportista (médico, competencias y bienestar). | Entrenador (nuevo) |
| **RF-021** | Crear Rutina Técnica | El entrenador crea un plan de entrenamiento (texto o PDF) con fechas de inicio y fin, y se lo asigna a sus atletas. | Entrenador |
| **RF-022** | Ver Rutina Técnica | El deportista puede consultar sus rutinas asignadas en modo solo lectura (sin modificarlas). | Deportista, Entrenador, Admin |
| **RF-023** | Registrar Asistencia a Entrenamiento | El deportista registra la fecha, duración y qué tan difícil (del 1 al 10) sintió su entrenamiento del día. | Deportista |
| **RF-024** | Visualizar Reporte de Asistencias | El entrenador ve el porcentaje de asistencia mensual y recibe alertas si el atleta falta mucho o muestra desmotivación. | Entrenador |
| **RF-025** | Agregar Deportista al Equipo | El entrenador añade a un deportista directamente a su equipo y el sistema le envía un aviso automático al atleta. | Entrenador |
| **RF-026** | Solicitar Unirse a un Equipo | El deportista envía una solicitud para entrar a un equipo y el entrenador decide si lo acepta o lo rechaza. | Deportista |
| **RF-027** | Notificar Solicitud de Equipo | El sistema envía un mensaje por WhatsApp o correo al entrenador cuando un deportista pide unirse a su equipo. | Sistema (automático) |
| **RF-028** | Auto-gestionar Perfil Personal | El deportista puede actualizar sus datos de contacto y foto, pero no sus datos oficiales (cédula o fecha de nacimiento). | Deportista |
| **RF-029** | Ver Información Personal | Permite ver perfiles según el rol: cada deportista ve el suyo, el entrenador ve a sus atletas y el administrador ve todo. | Deportista, Entrenador, Admin |

---

## 8. Requisitos de Sistema – No Funcionales

| ID Requisito | Tipo de RNF | Descripción del Requisito |
| :--- | :--- | :--- |
| **RNF-001** | Rendimiento - Búsqueda | Búsqueda de atleta por nombre/documento: <2 segundos (para 2000 usuarios). |
| **RNF-002** | Rendimiento - Restricciones | Listar restricciones activas de un deportista: <2 segundos. |
| **RNF-003** | Rendimiento - Historial | Consultar historial de bienestar (últimos 12 meses): <3 segundos. |
| **RNF-004** | Rendimiento - Carga de Página | Carga inicial de dashboard: <2 segundos (con caché). |
| **RNF-005** | Seguridad - Ley 1581 | Cumplir Ley 1581 de Protección de Datos Personales. |
| **RNF-006** | Seguridad - Contraseñas | Contraseñas hasheadas con bcrypt (mín 12 caracteres, complejidad requerida). |
| **RNF-007** | Seguridad - Encriptación en Reposo | Datos médicos (`Novedad_Medica`, `Reporte_Bienestar`) encriptados con AES-256 en base de datos. |
| **RNF-008** | Usabilidad - Inscripción | Máximo 4 clics para inscribir deportista a competencia (si no tiene restricción). |
| **RNF-009** | Usabilidad - Alertas Visuales | Restricciones activas mostradas en **ROJO** en todas las vistas relevantes. Sin ambigüedad. |
| **RNF-010** | Mantenibilidad - Versionado | Código versionado en GitHub con commits descriptivos. |
| **RNF-011** | Mantenibilidad - Documentación | Documentación de API. |
| **RNF-012** | Interoperabilidad - Notificaciones | Sistema envía notificaciones vía email + WhatsApp (Twilio o similar). Fallback a email si WhatsApp no está disponible. |

---

## 9. Reglas de Negocio

| ID RN | Descripción de la Regla | Impacto | Prioridad |
| :--- | :--- | :--- | :--- |
| **RN-001** | Validación de Restricción en Inscripción: Si `Novedad_Medica` está confirmada y tiene restricciones no permitir inscripción a menos que Deportista adjunte documento "Apto para competir" / autorización | Garantiza seguridad del atleta; previene lesiones agravadas. | **CRÍTICA** |
| **RN-002** | Los datos médicos son privados. Solo los ven el propio deportista, su entrenador, y los administradores. Los monitores y otros deportistas jamás pueden ver los datos médicos de los demás. | Privacidad; protege datos sensibles. | **CRÍTICA** |
| **RN-003** | Una persona puede tener más de un rol a la vez (por ejemplo, ser deportista y monitor). Los permisos se suman, pero respetando las reglas de privacidad (como monitor ve la información de su grupo, pero como deportista solo ve sus propios datos). | Flexibilidad operativa; permite atletas-monitores. | **ALTA** |
| **RN-004** | Los entrenadores, monitores y administradores pueden crear torneos. Para que el torneo permita inscripciones, primero se debe definir al menos una modalidad y una categoría. El administrador puede exigir su aprobación antes de publicarlo. | Asegura completitud de datos; previene torneos mal configurados. | **ALTA** |
| **RN-005** | Un deportista solicita unirse a un equipo y queda en espera. El entrenador revisa la solicitud y la acepta o la rechaza. El entrenador también puede agregar deportistas directamente si lo desea. | Flujo transparente; auditable; respeta autonomía. | **ALTA** |
| **RN-006** | El sistema guarda la fecha y hora exacta con máximo detalle cada vez que se crea o modifica información importante para saber siempre cuándo ocurrió cada cambio. | Precisión temporal; cumplimiento legal; reconstrucción de eventos. | **CRÍTICA** |
| **RN-007** | Queda grabado un historial de quién hizo qué en las tablas importantes (qué cambió, quién lo hizo y cuándo). Este registro se guarda mínimo un año por ley. | Cumplimiento legal; accountabilidad; trazabilidad. | **CRÍTICA** |
| **RN-008** | El sistema envía avisos por correo o WhatsApp cuando te inscribes a un torneo, cuando hay un problema de salud grave, cuando alguien pide unirse a un equipo o para recordarte llenar tu reporte diario. | Comunicación oportuna; previene pérdida de información. | **ALTA** |
| **RN-009** | Si a un deportista le asignan un nuevo entrenador, el historial completo del atleta pasa al nuevo entrenador sin borrar nada y dejando registro del cambio. | Preserva memoria institucional; evita re-onboarding. | **CRÍTICA** |
| **RN-010** | La medalla (Oro, Plata o Bronce) no se escribe manualmente en la base de datos; el sistema la calcula automáticamente según la posición (1°, 2° o 3°). | Normalización BD; integridad referencial. | **MEDIA** |
| **RN-011** | El número total de participantes de una categoría no se digita a mano; el sistema cuenta automáticamente las inscripciones activas. | Normalización BD; evita caché manual. | **MEDIA** |
| **RN-012** | El deportista puede cambiar sus datos de contacto (teléfono, correo, foto, dirección), pero no puede cambiar datos oficiales como cédula, fecha de nacimiento o género (eso solo lo hace un administrador). | Balance entre usabilidad y control; Ley 1581 compliance. | **MEDIA** |
| **RN-013** | Las restricciones médicas no se borran cuando el deportista se recupera; quedan guardadas marcadas como "resueltas" para conservar todo el historial de salud. | Trazabilidad; cumplimiento legal; análisis de patrones. | **ALTA** |
| **RN-014** | Si un deportista reporta baja motivación o mucha dificultad en tres entrenamientos seguidos, el sistema le envía una alerta al entrenador para que pueda apoyarlo. | Detección temprana de problemas; apoyo proactivo. | **MEDIA** |
| **RN-015** | Pasadas 48 horas, nadie puede cambiar el resultado de una competencia. Si un administrador necesita corregirlo después de ese tiempo, debe justificar por escrito el motivo del cambio. | Evita manipulación de datos; Ley 1581 compliance. | **ALTA** |

---

## Referencia

- [[20 - PROYECTOS/GestionDeportiva/GestionDeportiva]] — epicentro del módulo deportivo
- [[20 - PROYECTOS/SIMAD-ITM/SIMAD-ITM]] — proyecto hermano del sistema integral
- [[20 - PROYECTOS/GestionDeportiva/Diccionario de Entidades - GestionDeportiva]] — modelo de datos que implementa estos requisitos
- [[Autenticación-y-Autorización]] — requisitos RNF-006 y roles RU-001 a RU-004

#gestion-deportiva #simad #narrativa #requisitos #itm #deporte
