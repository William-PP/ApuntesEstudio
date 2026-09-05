---
type: documento
state: activa
priority: alta
created: 2026-08-30
updated: 2026-09-05
related: [GestionDeportiva, SIMAD]
tags: [gestion-deportiva, diccionario-datos, base-datos, modelo-relacional, simad]
aliases: [Diccionario GestionDeportiva, Diccionario de Entidades GestionDeportiva]
---

# Diccionario de Entidades — Sistema de Gestión Deportiva

> [!info] Contexto
> Diccionario del modelo relacional del **módulo deportivo** de SIMAD, organizado en 5 niveles de funcionalidad. Documento compartido con el proyecto hermano [[20 - PROYECTOS/SIMAD/SIMAD]].

---

## Nivel 1 — Identidad

---

### Persona

Registro base de toda persona en el sistema. Entrenadores y Deportistas extienden esta entidad.

**Atributos**

| Atributo | Tipo | Descripción |
|---|---|---|
| id_persona | int (PK) | Identificador único interno |
| numero_documento | varchar (UQ) | Cédula o documento de identidad — no se repite |
| tipo_documento | varchar | CC, TI, CE, etc. |
| nombre | varchar | Nombres |
| apellido | varchar | Apellidos |
| email_personal | varchar (UQ) | Correo personal — no se repite |
| telefono | varchar | Número de contacto |
| fecha_nacimiento | date | Fecha de nacimiento |
| genero | varchar | Género de la persona |
| direccion | varchar | Dirección de residencia |
| ciudad | varchar | Ciudad de residencia |
| foto_url | varchar | Ruta de la foto de perfil |
| timestamp_creacion | datetime | Cuándo se creó el registro |
| timestamp_modificacion | datetime | Última vez que se modificó |

**Métodos**

| Método | Qué hace |
|---|---|
| obtenerDatosPersonales() | Devuelve los datos del perfil |
| actualizarContacto() | Actualiza teléfono, correo o dirección |
| registrarTimestamp() | Actualiza automáticamente las fechas de cambio |
| auditar() | Registra en auditoría quién hizo un cambio y qué cambió |

**Relaciones**

| Con quién | Cardinalidad | Descripción |
|---|---|---|
| Entrenador | 1 a 1 | Una persona puede ser entrenador |
| Deportista | 1 a 1 | Una persona puede ser deportista |
| Usuario | 1 a 1 | Una persona tiene una cuenta de acceso |

---

### Entrenador

Extiende a Persona. Contiene los datos profesionales del entrenador y sus acciones dentro del sistema.

**Atributos**

| Atributo | Tipo | Descripción |
|---|---|---|
| id_persona | int (PK, FK) | Mismo ID que en Persona |
| especialidad | varchar | Deporte o disciplina en la que se especializa |
| fecha_certificacion | date | Fecha de su certificación profesional |
| institucion_formadora | varchar | Institución donde se certificó |
| anos_experiencia | int | Años de experiencia |
| estado | varchar | Activo o inactivo en el sistema |
| timestamp_creacion | datetime | Cuándo se registró como entrenador |
| timestamp_modificacion | datetime | Última modificación del registro |

**Métodos**

| Método | Qué hace |
|---|---|
| crearTorneo() | Crea un nuevo torneo |
| configurarModalidadesTorneo() | Asigna modalidades al torneo |
| configurarCategoriasTorneo() | Asigna categorías al torneo |
| registrarInscripcion() | Inscribe a un deportista en un torneo |
| registrarResultado() | Registra el resultado de un deportista en competencia |
| validarRestriccionMedica() | Consulta si un deportista tiene restricción activa |
| registrarRestriccionMedica() | Crea una restricción médica formal para un deportista |
| levantarRestriccion() | Marca una restricción como resuelta cuando el atleta se recupera |
| visualizarRestriccionesActivas() | Lista los deportistas con restricciones activas |
| confirmarNovedadMedica() | Aprueba o rechaza una novedad médica reportada por el deportista |
| crearRutinaTecnica() | Crea y asigna un plan de entrenamiento |
| visualizarReporteAsistencias() | Ve el porcentaje de asistencia mensual de un deportista |
| agregarDeportista() | Añade directamente un deportista a su equipo |
| enviarAlerta() | Envía una alerta al deportista (ej. baja motivación) |
| transferirHistorialDeportista() | Transfiere todo el historial de un atleta a otro entrenador |

**Relaciones**

| Con quién | Cardinalidad | Descripción |
|---|---|---|
| Persona | 1 a 1 | Hereda de Persona (TPT) |
| Equipo | 1 a muchos | Un entrenador puede dirigir varios equipos |
| Rutina_Tecnico | 1 a muchos | Crea y asigna varias rutinas |

---

### Deportista

Extiende a Persona. Contiene datos físicos y deportivos del atleta, y sus acciones dentro del sistema.

**Atributos**

| Atributo | Tipo | Descripción |
|---|---|---|
| id_persona | int (PK, FK) | Mismo ID que en Persona |
| deporte | varchar | Deporte principal que practica |
| categoria_competencia | varchar | Categoría en la que compite habitualmente |
| posicion_especialidad | varchar | Posición o especialidad dentro de su deporte |
| nivel_competencia | varchar | Nivel (recreativo, competitivo, élite) |
| peso | decimal | Peso en kg |
| altura | decimal | Altura en cm |
| tipo_sangre | varchar | Grupo sanguíneo |
| fecha_inicio_programa | date | Cuándo empezó en el programa de ITM |
| estado | varchar | Activo, inactivo, suspendido |
| avanzado | boolean | Si está en nivel avanzado del programa |
| timestamp_creacion | datetime | Cuándo se registró como deportista |
| timestamp_modificacion | datetime | Última modificación del registro |

**Métodos**

| Método | Qué hace |
|---|---|
| autoInscribirse() | Se inscribe solo a un torneo |
| registrarObservacionesCompetencia() | Escribe sus propias observaciones post-competencia |
| completarFormularioBienestar() | Llena el reporte diario de bienestar |
| reportarNovedadMedica() | Reporta una lesión, enfermedad o alergia |
| registrarAsistenciaEntrenamiento() | Registra que asistió a una sesión y qué tan difícil fue |
| solicitarUnirseEquipo() | Envía solicitud para entrar a un equipo |
| verRutinasAsignadas() | Consulta sus planes de entrenamiento |
| verHistorialBienestar() | Revisa sus reportes anteriores de bienestar |
| actualizarDatosContacto() | Actualiza su correo, teléfono, dirección o foto |
| verResultadosCompetencia() | Consulta sus resultados en torneos |
| verificarRestriccionesActivas() | Consulta si tiene restricciones médicas vigentes |

**Relaciones**

| Con quién | Cardinalidad | Descripción |
|---|---|---|
| Persona | 1 a 1 | Hereda de Persona (TPT) |
| Equipo_Deportista | 1 a muchos | Puede pertenecer a varios equipos a lo largo del tiempo |
| Inscripcion | 1 a muchos | Puede inscribirse a varios torneos |
| Novedad_Medica | 1 a muchos | Puede tener varias novedades médicas registradas |
| Reporte_Bienestar | 1 a muchos | Genera reportes periódicos |
| Asistencia_Entrenamiento | 1 a muchos | Registra múltiples asistencias |
| Rutina_Tecnico | 1 a muchos | Puede tener varias rutinas asignadas |
| SolicitudEquipo | 1 a muchos | Puede enviar varias solicitudes para unirse a equipos |

---

## Nivel 2 — Acceso y Permisos

---

### Usuario

Cuenta de acceso al sistema. Cada usuario está vinculado a una Persona.

**Atributos**

| Atributo | Tipo | Descripción |
|---|---|---|
| id_usuario | int (PK) | Identificador único de la cuenta |
| numero_documento | varchar (UQ, FK) | Vincula la cuenta con una Persona |
| email_usuario | varchar (UQ) | Correo con el que inicia sesión |
| password_hash | varchar | Contraseña cifrada |
| activo | boolean | Si la cuenta está habilitada |
| fecha_ultimo_login | datetime | Última vez que inició sesión |
| timestamp_creacion | datetime | Cuándo se creó la cuenta |
| timestamp_modificacion | datetime | Última modificación de la cuenta |

**Métodos**

| Método | Qué hace |
|---|---|
| validarCredenciales() | Verifica que el correo y contraseña sean correctos |
| autenticar() | Genera el token de sesión |
| cambiarPassword() | Cambia la contraseña actual |
| registrarUltimoLogin() | Guarda la fecha y hora del último ingreso |
| verificarPermiso() | Comprueba si el usuario tiene acceso a una acción específica |
| obtenerRolesAsignados() | Lista los roles activos del usuario |

**Relaciones**

| Con quién | Cardinalidad | Descripción |
|---|---|---|
| Persona | 1 a 1 | Cada cuenta pertenece a una persona |
| Usuario_Rol | 1 a muchos | Un usuario puede tener varios roles |
| Resultado | 1 a muchos | Registra resultados de competencia |
| Auditoria | 1 a muchos | Sus acciones quedan en auditoría |
| Notificacion | 1 a muchos | Recibe notificaciones del sistema |

---

### Rol_Permisos

Define los roles del sistema (Deportista, Entrenador, Monitor, Administrador) y los permisos que cada uno incluye. Una misma tabla almacena tanto roles como permisos individuales, distinguidos por el campo `tipo`.

**Atributos**

| Atributo | Tipo | Descripción |
|---|---|---|
| id_rol | int (PK) | Identificador del rol o permiso |
| nombre_rol | varchar (UQ) | Nombre — ej. "ENTRENADOR", "VER_HISTORIAL_MEDICO" |
| descripcion | text | Descripción de qué permite este rol o permiso |
| tipo | varchar | ROL o PERMISO |
| activo | boolean | Si está habilitado actualmente |

**Métodos**

| Método | Qué hace |
|---|---|
| obtenerPermisosDelRol() | Lista todos los permisos asociados a un rol |

**Relaciones**

| Con quién | Cardinalidad | Descripción |
|---|---|---|
| Usuario_Rol | 1 a muchos | Muchos usuarios pueden tener este rol |

---

### Usuario_Rol

Tabla intermedia que vincula usuarios con sus roles. Permite que una persona tenga más de un rol al mismo tiempo.

**Atributos**

| Atributo | Tipo | Descripción |
|---|---|---|
| id_usuario | int (PK, FK) | Usuario al que se asigna el rol |
| id_rol | int (PK, FK) | Rol o permiso asignado |
| fecha_asignacion | datetime | Cuándo se asignó el rol |
| activo | boolean | Si la asignación sigue vigente |

**Métodos**

| Método | Qué hace |
|---|---|
| asignarRol() | Activa la relación usuario-rol |
| revocarRol() | Desactiva la relación |
| sumarPermisos() | Consolida todos los permisos del usuario sumando sus roles |

**Relaciones**

| Con quién | Cardinalidad | Descripción |
|---|---|---|
| Usuario | muchos a 1 | Varios roles pertenecen a un usuario |
| Rol_Permisos | muchos a 1 | Varios usuarios pueden tener el mismo rol |

---

## Nivel 3 — Estructura Deportiva

---

### Equipo

Agrupa deportistas bajo la dirección de un entrenador.

**Atributos**

| Atributo | Tipo | Descripción |
|---|---|---|
| id_equipo | int (PK) | Identificador del equipo |
| nombre | varchar | Nombre del equipo |
| id_entrenador | int (FK) | Entrenador responsable |
| descripcion | text | Descripción del equipo |
| deporte | varchar | Deporte que practica el equipo |
| activo | boolean | Si el equipo está activo actualmente |
| timestamp_creacion | datetime | Cuándo se creó el equipo |
| timestamp_modificacion | datetime | Última modificación |

**Métodos**

| Método | Qué hace |
|---|---|
| obtenerDetalles() | Devuelve la información del equipo |
| listarDeportistas() | Lista todos los deportistas actuales del equipo |
| cambiarEntrenador() | Reasigna el equipo a otro entrenador |

**Relaciones**

| Con quién | Cardinalidad | Descripción |
|---|---|---|
| Entrenador | muchos a 1 | Un entrenador dirige el equipo |
| Equipo_Deportista | 1 a muchos | Un equipo tiene varios deportistas |
| SolicitudEquipo | 1 a muchos | Recibe solicitudes de deportistas para unirse |

---

### Equipo_Deportista

Tabla intermedia que registra la pertenencia de un deportista a un equipo, incluyendo historial de cambios.

**Atributos**

| Atributo | Tipo | Descripción |
|---|---|---|
| id_equipo | int (PK, FK) | Equipo al que pertenece |
| id_deportista | int (PK, FK) | Deportista que pertenece al equipo |
| fecha_inicio | date | Desde cuándo está en el equipo |
| fecha_fin | date | Hasta cuándo estuvo (null si sigue activo) |
| estado | varchar | Activo, transferido, retirado |

**Métodos**

| Método | Qué hace |
|---|---|
| cambiarEquipo() | Registra el traslado del deportista a otro equipo |
| validarTraslape() | Verifica que no haya dos membresías activas al mismo tiempo |

**Relaciones**

| Con quién | Cardinalidad | Descripción |
|---|---|---|
| Equipo | muchos a 1 | Pertenece a un equipo |
| Deportista | muchos a 1 | Corresponde a un deportista |

---

### SolicitudEquipo

Registra las solicitudes que envía un deportista para unirse a un equipo.

**Atributos**

| Atributo | Tipo | Descripción |
|---|---|---|
| id_solicitud | int (PK) | Identificador de la solicitud |
| id_deportista | int (FK) | Deportista que solicita |
| id_equipo | int (FK) | Equipo al que quiere unirse |
| fecha_solicitud | datetime | Cuándo envió la solicitud |
| estado | varchar | pendiente, aceptada, rechazada |
| timestamp_creacion | datetime | Cuándo se creó el registro |
| timestamp_modificacion | datetime | Última modificación |

**Métodos**

| Método | Qué hace |
|---|---|
| aceptarSolicitud() | El entrenador acepta al deportista en el equipo |
| rechazarSolicitud() | El entrenador rechaza la solicitud |
| notificarEntrenador() | Envía aviso al entrenador cuando llega una solicitud |

**Relaciones**

| Con quién | Cardinalidad | Descripción |
|---|---|---|
| Deportista | muchos a 1 | Un deportista puede hacer varias solicitudes |
| Equipo | muchos a 1 | Un equipo puede recibir varias solicitudes |

---

## Nivel 4 — Eventos y Competencias

---

### Modalidad

Catálogo de las disciplinas disponibles en el sistema (atletismo, natación, etc.).

**Atributos**

| Atributo | Tipo | Descripción |
|---|---|---|
| id_modalidad | int (PK) | Identificador de la modalidad |
| nombre_modalidad | varchar | Nombre de la disciplina |
| descripcion | text | Descripción de la modalidad |
| activa | boolean | Si está disponible para usarse en torneos |

**Métodos**

| Método | Qué hace |
|---|---|
| obtenerDetalles() | Devuelve la información de la modalidad |

**Relaciones**

| Con quién | Cardinalidad | Descripción |
|---|---|---|
| TorneoModalidad | 1 a muchos | Puede estar asignada a varios torneos |
| Inscripcion | 1 a muchos | Los deportistas se inscriben en una modalidad |

---

### Categoria

Catálogo de categorías de competencia definidas por rango de edad y peso.

**Atributos**

| Atributo | Tipo | Descripción |
|---|---|---|
| id_categoria | int (PK) | Identificador de la categoría |
| nombre | varchar | Nombre descriptivo (ej. "Sub-23") |
| rango_edad_minimo | int | Edad mínima para aplicar |
| rango_edad_maximo | int | Edad máxima para aplicar |
| peso_minimo | decimal | Peso mínimo en kg |
| peso_maximo | decimal | Peso máximo en kg |
| activa | boolean | Si está disponible para usarse |

**Métodos**

| Método | Qué hace |
|---|---|
| validarCategoriaDeportista() | Verifica si un deportista cumple los requisitos de edad y peso |
| obtenerDetalles() | Devuelve la información de la categoría |

**Relaciones**

| Con quién | Cardinalidad | Descripción |
|---|---|---|
| TorneoCategoria | 1 a muchos | Puede asignarse a varios torneos |
| Inscripcion | 1 a muchos | Los deportistas se inscriben bajo una categoría |

---

### Torneo

Representa una competencia formal organizada en ITM.

**Atributos**

| Atributo | Tipo | Descripción |
|---|---|---|
| id_torneo | int (PK) | Identificador del torneo |
| nombre | varchar | Nombre del torneo |
| fecha_inicio | date | Fecha de inicio |
| fecha_fin | date | Fecha de cierre |
| sede | varchar | Lugar donde se realiza |
| tipo_torneo | varchar | Formal, amistoso, interno, etc. |
| estado | varchar | Borrador, publicado, en curso, finalizado |
| timestamp_creacion | datetime | Cuándo se creó el torneo |
| timestamp_modificacion | datetime | Última modificación |

**Métodos**

| Método | Qué hace |
|---|---|
| obtenerDetalles() | Devuelve la información del torneo |
| validarConfiguracion() | Verifica que el torneo esté bien configurado |
| validarMinimosRequeridos() | Comprueba que tenga al menos una modalidad y una categoría antes de publicarse |
| listarInscripciones() | Lista todos los inscritos al torneo |

**Relaciones**

| Con quién | Cardinalidad | Descripción |
|---|---|---|
| TorneoModalidad | 1 a muchos | Tiene una o más modalidades asignadas |
| TorneoCategoria | 1 a muchos | Tiene una o más categorías asignadas |
| Inscripcion | 1 a muchos | Recibe inscripciones de deportistas |

---

### TorneoModalidad

Tabla intermedia que asigna modalidades a un torneo específico.

**Atributos**

| Atributo | Tipo | Descripción |
|---|---|---|
| id_torneo | int (PK, FK) | Torneo al que pertenece |
| id_modalidad | int (PK, FK) | Modalidad asignada |
| activa | boolean | Si está habilitada en ese torneo |
| timestamp_creacion | datetime | Cuándo se hizo la asignación |

**Métodos**

| Método | Qué hace |
|---|---|
| activarDesactivar() | Habilita o deshabilita la modalidad dentro del torneo |

**Relaciones**

| Con quién | Cardinalidad | Descripción |
|---|---|---|
| Torneo | muchos a 1 | Pertenece a un torneo |
| Modalidad | muchos a 1 | Referencia una modalidad del catálogo |

---

### TorneoCategoria

Tabla intermedia que asigna categorías a un torneo específico.

**Atributos**

| Atributo | Tipo | Descripción |
|---|---|---|
| id_torneo | int (PK, FK) | Torneo al que pertenece |
| id_categoria | int (PK, FK) | Categoría asignada |
| activa | boolean | Si está habilitada en ese torneo |
| timestamp_creacion | datetime | Cuándo se hizo la asignación |

**Métodos**

| Método | Qué hace |
|---|---|
| activarDesactivar() | Habilita o deshabilita la categoría dentro del torneo |
| contarInscripciones() | Cuenta cuántos deportistas están inscritos en esta categoría — calculado automáticamente |

**Relaciones**

| Con quién | Cardinalidad | Descripción |
|---|---|---|
| Torneo | muchos a 1 | Pertenece a un torneo |
| Categoria | muchos a 1 | Referencia una categoría del catálogo |

---

### Inscripcion

Registra la participación de un deportista en un torneo, bajo una modalidad y categoría específicas.

**Atributos**

| Atributo | Tipo | Descripción |
|---|---|---|
| id_inscripcion | int (PK) | Identificador de la inscripción |
| id_deportista | int (FK) | Deportista inscrito |
| id_torneo | int (FK) | Torneo al que se inscribió |
| id_modalidad | int (FK) | Modalidad en la que participa |
| id_categoria | int (FK) | Categoría bajo la que compite |
| fecha_inscripcion | datetime | Cuándo se realizó la inscripción |
| estado | varchar | Activa, cancelada, bloqueada |
| posicion_sorteo | int | Número de sorteo asignado |
| timestamp_creacion | datetime | Cuándo se creó el registro |
| timestamp_modificacion | datetime | Última modificación |

**Métodos**

| Método | Qué hace |
|---|---|
| validarInscripcion() | Verifica que el deportista cumpla todos los requisitos |
| validarRestriccionMedica() | Consulta si tiene una restricción médica activa |
| validarAptoCompetitivo() | Verifica si presentó el aval médico para competir |
| obtenerDetalles() | Devuelve la información de la inscripción |
| cancelarInscripcion() | Cancela la participación del deportista |
| notificarDeportista() | Envía aviso al deportista confirmando su inscripción |

**Relaciones**

| Con quién | Cardinalidad | Descripción |
|---|---|---|
| Deportista | muchos a 1 | Un deportista puede tener varias inscripciones |
| Torneo | muchos a 1 | Un torneo recibe múltiples inscripciones |
| Modalidad | muchos a 1 | La inscripción es en una modalidad |
| Categoria | muchos a 1 | La inscripción es bajo una categoría |
| Resultado | 1 a 1 | Cada inscripción tiene a lo sumo un resultado |

---

### Resultado

Registra el desempeño de un deportista en una competencia. La medalla se calcula automáticamente según la posición.

**Atributos**

| Atributo | Tipo | Descripción |
|---|---|---|
| id_resultado | int (PK) | Identificador del resultado |
| id_inscripcion | int (UQ, FK) | Inscripción a la que corresponde |
| tiempo | decimal | Tiempo registrado en la prueba |
| posicion | int | Lugar en que terminó |
| medalla | varchar | Oro, Plata, Bronce — calculado automáticamente por posición |
| puntos | int | Puntos obtenidos |
| observaciones_deportista | varchar | Notas del propio deportista sobre su participación |
| observaciones_entrenador | varchar | Notas técnicas del entrenador |
| timestamp_registro | datetime | Cuándo se registró el resultado |
| timestamp_modificacion | datetime | Última modificación — relevante para la regla de 48 horas |
| registrado_por | int (FK) | Usuario que ingresó el resultado |
| fecha_registro_original | datetime | Fecha original de registro, no se modifica |

**Métodos**

| Método | Qué hace |
|---|---|
| registrarResultado() | Crea el registro de resultado en el sistema |
| calcularMedalla() | Asigna automáticamente la medalla según la posición |
| validarTiempoModificacion() | Verifica si han pasado más de 48 horas desde el registro |
| permitirModificacion() | Autoriza una corrección tardía si se justifica por escrito |
| obtenerDetalles() | Devuelve la información completa del resultado |
| registrarObservaciones() | Agrega observaciones al resultado existente |

**Relaciones**

| Con quién | Cardinalidad | Descripción |
|---|---|---|
| Inscripcion | 1 a 1 | Un resultado corresponde a una inscripción |
| Usuario | muchos a 1 | El resultado fue registrado por un usuario |

---

## Nivel 5 — Gestión Continua

---

### Novedad_Medica

Registra lesiones, enfermedades o alergias reportadas por el deportista o el entrenador. Las restricciones nunca se eliminan — quedan marcadas como resueltas.

**Atributos**

| Atributo | Tipo | Descripción |
|---|---|---|
| id_novedad | int (PK) | Identificador de la novedad |
| id_deportista | int (FK) | Deportista al que corresponde |
| descripcion | varchar | Descripción de la lesión o novedad |
| zona_afectada | varchar | Parte del cuerpo afectada |
| fecha_inicio | date | Cuándo inició la novedad |
| fecha_resolucion | date | Fecha estimada de recuperación |
| estado | varchar | pendiente, confirmada, rechazada, resuelta |
| tipo_novedad | varchar | Lesión, enfermedad, alergia |
| reportador_medico | varchar | Médico o profesional que emitió la incapacidad |
| incapacidad | varchar | Referencia al documento de incapacidad |
| restricciones_activas | varchar | Descripción de lo que no puede hacer el deportista |
| aval_competitivo | boolean | Si tiene permiso médico para competir |
| aval_foto | varchar | Ruta del documento de aval subido |
| confirmacion_entrenador | boolean | Si el entrenador confirmó la novedad |
| observaciones_entrenador | varchar | Notas del entrenador sobre la novedad |
| timestamp_creacion | datetime | Cuándo se reportó la novedad |
| timestamp_modificacion | datetime | Última modificación |

**Métodos**

| Método | Qué hace |
|---|---|
| validarRestriccionActiva() | Revisa si la restricción sigue vigente por fecha |
| tieneAvalCompetitivo() | Comprueba si el deportista puede competir a pesar de la restricción |
| confirmarPorEntrenador() | El entrenador aprueba la novedad y agrega observaciones |
| rechazarSolicitudAval() | El entrenador rechaza el aval para competir |
| marcarResuelta() | Cierra la novedad cuando el deportista se recupera |
| verificarVigencia() | Calcula si la fecha de resolución ya pasó |
| obtenerDetalles() | Devuelve la información de la novedad |
| bloquearInscripcion() | Impide que el deportista se inscriba si tiene restricción activa sin aval |

**Relaciones**

| Con quién | Cardinalidad | Descripción |
|---|---|---|
| Deportista | muchos a 1 | Un deportista puede tener varias novedades médicas |

---

### Reporte_Bienestar

Registro periódico del estado físico y mental del deportista. Se llena con frecuencia diaria.

**Atributos**

| Atributo | Tipo | Descripción |
|---|---|---|
| id_reporte | int (PK) | Identificador del reporte |
| id_deportista | int (FK) | Deportista que reporta |
| fecha_digitacion | date | Fecha del reporte |
| sueno | int (1-10) | Calidad del sueño |
| fatiga | int (1-10) | Nivel de cansancio |
| dolor_muscular | int (1-10) | Intensidad del dolor muscular |
| estres | int (1-10) | Nivel de estrés |
| molestias | boolean | Si tiene alguna molestia específica |
| peso | decimal | Peso del día (opcional) |
| lugar_molestias | varchar | Zona con molestia (si aplica) |
| incapacita_molestia | boolean | Si la molestia le impide entrenar |
| observaciones | text | Notas adicionales (opcional) |
| adjunto_foto | boolean | Si subió un archivo como evidencia |
| ruta_archivo | varchar | Ruta del archivo adjunto |
| timestamp_creacion | datetime | Cuándo se creó el reporte |
| timestamp_modificacion | datetime | Última modificación |

**Métodos**

| Método | Qué hace |
|---|---|
| validarFormulario() | Verifica que los campos obligatorios estén completos |
| detectarAlertaBajaMotivacion() | Detecta si hay 3 reportes seguidos con dificultad alta o baja motivación |
| enviarNotificacionEntrenador() | Alerta al entrenador cuando se detecta un patrón preocupante |
| obtenerDetalles() | Devuelve el reporte completo |
| generarTrendingBienestar() | Muestra la evolución del bienestar del deportista en el tiempo |

**Relaciones**

| Con quién | Cardinalidad | Descripción |
|---|---|---|
| Deportista | muchos a 1 | Un deportista genera muchos reportes |

---

### Asistencia_Entrenamiento

Registra cada sesión de entrenamiento a la que asistió el deportista.

**Atributos**

| Atributo | Tipo | Descripción |
|---|---|---|
| id_AsistenciaEntrenamiento | int (PK) | Identificador del registro |
| id_deportista | int (FK) | Deportista que asistió |
| fecha_entrenamiento | datetime | Fecha y hora de la sesión |
| tipo_entrenamiento | varchar | Tipo de sesión (fuerza, cardio, técnica, etc.) |
| duracion_entrenamiento | int | Duración en minutos |
| dificultad | int (1-10) | Qué tan difícil sintió el entrenamiento |
| timestamp_creacion | datetime | Cuándo se registró la asistencia |
| timestamp_modificacion | datetime | Última modificación |

**Métodos**

| Método | Qué hace |
|---|---|
| registrarAsistencia() | Crea el registro de asistencia del día |
| obtenerDetalles() | Devuelve la información de la sesión |
| calcularPorcentajeAsistencia() | Calcula el porcentaje de asistencia de un mes determinado |

**Relaciones**

| Con quién | Cardinalidad | Descripción |
|---|---|---|
| Deportista | muchos a 1 | Un deportista tiene muchas asistencias registradas |

---

### Rutina_Tecnico

Plan de entrenamiento que el entrenador crea y asigna a un deportista.

**Atributos**

| Atributo | Tipo | Descripción |
|---|---|---|
| id_seguimiento | int (PK) | Identificador de la rutina |
| id_deportista | int (FK) | Deportista al que está asignada |
| id_entrenador | int (FK) | Entrenador que la creó |
| plan | text | Descripción del plan en texto |
| plan_archivo | varchar | Ruta de un archivo PDF adjunto (opcional) |
| fecha_inicio_plan | date | Desde cuándo aplica la rutina |
| fecha_fin_plan | date | Hasta cuándo aplica |
| estado_plan | varchar | activa, finalizada, pausada |
| timestamp_creacion | datetime | Cuándo se creó la rutina |
| timestamp_modificacion | datetime | Última modificación |

**Métodos**

| Método | Qué hace |
|---|---|
| crearRutina() | Crea y guarda el plan de entrenamiento |
| obtenerDetalles() | Devuelve el contenido de la rutina |
| marcarComoLeida() | El deportista confirma que revisó la rutina |
| archivarRutina() | Finaliza la rutina sin eliminarla del historial |
| obtenerHistorialRutinas() | Lista todas las rutinas anteriores de un deportista |

**Relaciones**

| Con quién | Cardinalidad | Descripción |
|---|---|---|
| Deportista | muchos a 1 | Un deportista puede tener varias rutinas |
| Entrenador | muchos a 1 | Un entrenador crea varias rutinas |

---

### Auditoria

Registra todos los cambios importantes del sistema: quién hizo qué y cuándo. Se conserva mínimo un año.

**Atributos**

| Atributo | Tipo | Descripción |
|---|---|---|
| id_auditoria | int (PK) | Identificador del registro |
| tabla | varchar | Tabla donde ocurrió el cambio |
| operacion | varchar | INSERT, UPDATE o DELETE |
| id_registro | int | ID del registro afectado |
| usuario_id | int (FK) | Usuario que realizó la acción |
| cambios | text | Detalle de qué cambió (valores antes y después) |
| timestamp | datetime | Fecha y hora exacta del cambio |

**Métodos**

| Método | Qué hace |
|---|---|
| registrarCambio() | Guarda automáticamente cada modificación relevante |
| obtenerHistorialEntidad() | Lista todos los cambios de un registro específico |
| obtenerHistorialUsuario() | Lista todas las acciones de un usuario |
| generarReporteAuditoria() | Genera un reporte de actividad en un rango de fechas |
| validarRetencion() | Verifica que los registros cumplan el año mínimo de retención |

**Relaciones**

| Con quién | Cardinalidad | Descripción |
|---|---|---|
| Usuario | muchos a 1 | Cada registro de auditoría pertenece a un usuario |

---

### Notificacion

Gestiona los avisos que el sistema envía a los usuarios por correo, WhatsApp o notificación push.

**Atributos**

| Atributo | Tipo | Descripción |
|---|---|---|
| id_notificacion | int (PK) | Identificador de la notificación |
| id_usuario | int (FK) | Usuario destinatario |
| tipo | varchar | Tipo de aviso (recordatorio, alerta, confirmación) |
| mensaje | varchar | Contenido del mensaje |
| fecha_envio | datetime | Cuándo se envió o programó |
| canal | varchar | email, whatsapp, push |
| estado | varchar | pendiente, enviada, fallo |

**Métodos**

| Método | Qué hace |
|---|---|
| enviarNotificacion() | Despacha el mensaje por el canal indicado |
| registrarRecordatorioBienestar() | Envía el recordatorio diario de bienestar |
| notificarSolicitudEquipo() | Avisa al entrenador que un deportista quiere unirse a su equipo |
| notificarInscripcion() | Confirma al deportista su inscripción en un torneo |
| notificarAlerta() | Envía una alerta al entrenador (ej. baja motivación detectada) |

**Relaciones**

| Con quién | Cardinalidad | Descripción |
|---|---|---|
| Usuario | muchos a 1 | Cada notificación va dirigida a un usuario |

---

## Diagrama de niveles

```
┌──────────────────────────────────────────────────────────────┐
│  Nivel 1 — Identidad │ Nivel 2 — Acceso │ Nivel 3 — Estructura │
│  Persona             │ Usuario          │ Equipo                │
│  ├─ Entrenador       │ Rol_Permisos     │ Equipo_Deportista     │
│  └─ Deportista       │ Usuario_Rol      │ SolicitudEquipo       │
├──────────────────────┴──────────────────┴───────────────────────┤
│  Nivel 4 — Eventos y Competencias                               │
│  Torneo ⇄ TorneoModalidad ⇄ Modalidad                           │
│  Torneo ⇄ TorneoCategoria ⇄ Categoria                           │
│  Deportista → Inscripcion → Resultado                           │
├─────────────────────────────────────────────────────────────────┤
│  Nivel 5 — Gestión Continua                                     │
│  Novedad_Medica │ Reporte_Bienestar │ Asistencia_Entrenamiento  │
│  Rutina_Tecnico │ Auditoria         │ Notificacion              │
└─────────────────────────────────────────────────────────────────┘
```

> [!note] Diagrama de clases (UML)
> Versión visual del modelo en las siguientes imágenes (`.png` / `.svg`):
>
> `![](Diagrama de clases (GestionDeportiva).png)`

---

## Referencia

- [[20 - PROYECTOS/GestionDeportiva/GestionDeportiva]] — epicentro del módulo deportivo
- [[20 - PROYECTOS/SIMAD/SIMAD]] — proyecto hermano del sistema integral
- [[20 - PROYECTOS/GestionDeportiva/Narrativa - GestionDeportiva]] — requisitos que este modelo implementa
- [[Bases-Datos-Vulnerabilidades]] — consideraciones de seguridad del modelo de datos

#gestion-deportiva #simad #diccionario-datos #base-datos #modelo-relacional
