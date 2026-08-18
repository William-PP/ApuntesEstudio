---
type: concepto
state: activa
created: 2026-08-15
updated: 2026-08-15
tags: [ciberseguridad, hardening, linux, debian, nist, cis, sysadmin, kernel, firewall]
---

# Hardening 

Para aplicar hardening en un sistema como por ejemplo Debian 13 como práctica, podés seguir un camino estructurado basado en los marcos de trabajo de **NIST** y **CIS**, complementados con la guía de hardening de **Linux** de Madaidan's Insecurities. El hardening no es un evento único, sino un proceso continuo de reducción de la superficie de ataque para proteger la confidencialidad, integridad y disponibilidad de tus datos.

## Marcos de referencia

| Marco | Enfoque | Cómo se usa acá |
|-------|---------|-----------------|
| **CIS Controls v8.1** | 18 controles críticos de seguridad prioritarios con salvaguardas para proteger activos y datos en una empresa | Base de higiene esencial (IG1): parches, contraseñas, firewall, backups |
| **Guía de Hardening de Linux (Madaidan's Insecurities)** | Guía técnica agnóstica a la distribución con pasos para fortalecer la seguridad y privacidad del kernel y del SO Linux | Detalle técnico de las configuraciones de las Fases 3-5 |
| **NIST CSF 2.0** | Taxonomía de resultados de ciberseguridad: Gobernar, Identificar, Proteger, Detectar, Responder y Recuperar | Estructura el proceso: línea base, monitoreo, respuesta y recuperación |
| **NIST SP 800-53 Rev. 5** | Catálogo exhaustivo de controles de seguridad y privacidad para sistemas de información y organizaciones | Referencia formal de controles aplicables a cada fase |

## Fase 1: Identificación, Inventario y Planificación

La **Fase 1** es el pilar fundamental del proceso de hardening, ya que no es posible proteger activos que no han sido debidamente identificados. Basado en los marcos de **NIST** y **CIS**, esta etapa se desglosa en las siguientes acciones detalladas.

### 1. Inventario de Activos de Hardware

El primer paso consiste en establecer y mantener un inventario detallado de todos los activos físicos con potencial para almacenar o procesar datos.

| Acción | Detalle |
|--------|---------|
| **Identificación completa** | Registrar dispositivos finales (portátiles, móviles), dispositivos de red, servidores y equipos de Internet de las Cosas (IoT) |
| **Datos del registro** | Incluir la dirección de red (si es estática), la dirección de hardware (MAC), el nombre de la máquina y el propietario del activo |
| **Control de componentes** | Según NIST SP 800-53 (CM-8), el inventario debe reflejar con precisión el sistema para evitar que componentes no asignados se conviertan en vulnerabilidades desatendidas |

### 2. Inventario y Control de Software

Debés gestionar activamente el sistema operativo y todas las aplicaciones instaladas para asegurar que solo el software autorizado pueda ejecutarse.

| Acción | Detalle |
|--------|---------|
| **Documentación de licencias** | Registrar el título, el editor, la fecha de instalación y el propósito de negocio de cada software |
| **Gestión del ciclo de vida** | NIST recomienda mantener inventarios de servicios y sistemas para priorizar esfuerzos según la importancia relativa de cada aplicación para tus objetivos (ID.AM-02) |
| **Eliminación de software no autorizado** | Asegurar procesos para remover programas no permitidos o documentar excepciones justificadas mensualmente |

### 3. Clasificación y Gestión de Datos

Antes de aplicar medidas técnicas, es crucial identificar y priorizar la sensibilidad de la información almacenada.

| Acción | Detalle |
|--------|---------|
| **Esquema de clasificación** | Adoptar etiquetas como "Pública", "Confidencial" o "Sensible" para aplicar controles proporcionales al riesgo |
| **Mapeo de datos** | Identificar qué aplicaciones acceden a qué niveles de sensibilidad y en qué activos residen esos datos |
| **Categorización de seguridad** | NIST SP 800-53 (RA-2) detalla que este proceso ayuda a prever el impacto negativo si se compromete la confidencialidad, integridad o disponibilidad de la información |

### 4. Análisis de Riesgos y Modelado de Amenazas

Esta etapa te permite comprender el contexto de ciberseguridad específico de tu sistema para priorizar acciones.

| Acción | Detalle |
|--------|---------|
| **Identificación de amenazas** | Evaluar las vulnerabilidades de tus activos y los impactos potenciales de que una amenaza las explote |
| **Modelado técnico** | Análisis de vectores de ataque e identificación de controles compensatorios necesarios para mitigar riesgos específicos detectados en tu entorno |

### 5. Establecimiento de Gobernanza y Políticas

La función **GOBERNAR (GV)** de NIST CSF 2.0 establece que la estrategia de gestión de riesgos debe estar documentada y comunicada.

| Acción | Detalle |
|--------|---------|
| **Proceso de configuración segura** | Establecer y mantener un proceso documentado para la configuración segura de activos y software antes de realizar cualquier cambio técnico (CIS Control 4.1) |
| **Selección de la línea base** | Elegir un conjunto predefinido de controles (benchmarks) que sirvan como punto de partida para la protección del sistema |

> [!note] Resultado de la Fase 1
> Al finalizar esta fase, habrás reducido significativamente la incertidumbre sobre tu sistema y tendrás una **hoja de ruta clara**: la seguridad es, ante todo, un problema de diseño y planificación.

## Fase 2: Seguridad Física y del Proceso de Arranque

La **Fase 2** se centra en proteger el sistema antes de que el sistema operativo se haya cargado por completo, evitando que un atacante con acceso físico pueda comprometer la máquina. Según la **Guía de Hardening de Linux** y los marcos de **NIST** y **CIS**, las acciones clave son las siguientes.

### 1. Hardening de BIOS/UEFI y Firmware

El firmware es el primer código que se ejecuta al encender el computador y debe ser protegido para mantener la integridad de todo lo que sigue.

| Acción | Detalle |
|--------|---------|
| **Contraseña de Firmware** | Establecer una contraseña fuerte en el BIOS o UEFI para evitar que terceros cambien la configuración de arranque |
| **Deshabilitar Arranque Externo** | Desactivar la capacidad de arrancar desde dispositivos externos (USB, CD o red): permite iniciar un SO propio y saltarse todas las protecciones del disco |
| **Actualizaciones de Microcode** | Mantener actualizado el firmware y el microcódigo de la CPU para corregir vulnerabilidades críticas de hardware como Spectre o Meltdown |

### 2. Cifrado de Disco Completo (FDE)

El cifrado asegura que los datos en reposo sean ilegibles si el equipo es robado o el disco es extraído.

| Acción | Detalle |
|--------|---------|
| **Implementación de LUKS** | Usar **dm-crypt/LUKS** durante la instalación de Debian 13 para cifrar todas las particiones de datos |
| **Limitaciones** | El FDE por lo general no cubre la partición `/boot`: el kernel y el cargador de arranque quedan expuestos a manipulaciones si no se aplican medidas adicionales |

> [!warning] /boot sin cifrar
> El cifrado de disco completo no protege `/boot`. Por eso la Fase 2 combina FDE con contraseña de GRUB y Secure Boot: cada medida cubre la debilidad de la otra.

### 3. Protección del Cargador de Arranque (GRUB)

El cargador de arranque es un punto crítico donde un atacante físico puede inyectar parámetros para obtener una shell de root sin contraseña.

| Acción | Detalle |
|--------|---------|
| **Contraseña de GRUB** | Evita que se editen las entradas del menú (presionando `e`) para añadir comandos como `init=/bin/bash` |
| **Configuración en Debian** | Usar `grub-mkpasswd-pbkdf2` para generar un hash seguro de la contraseña y añadirlo al archivo de configuración de GRUB |

```bash
# Generar el hash PBKDF2 de la contraseña
grub-mkpasswd-pbkdf2

# Agregar el hash al archivo de configuración de GRUB (/etc/grub.d/00_header o /etc/default/grub)
# y luego regenerar la configuración
update-grub
```

### 4. Arranque Verificado (Verified Boot)

Esta medida asegura que cada componente del proceso de inicio sea verificado criptográficamente antes de ejecutarse.

| Acción | Detalle |
|--------|---------|
| **UEFI Secure Boot** | Implementación más común: verifica la firma digital del cargador de arranque y del kernel de Linux |
| **Cadena de Confianza** | Si un componente (ej. un módulo del kernel no firmado) fue alterado, el sistema se niega a arrancar, evitando la persistencia de malware a nivel de sistema |

### 5. Mitigación de Ataques Físicos Avanzados

Incluso con software seguro, el hardware puede ser vulnerable a ataques directos a la memoria.

| Acción | Detalle |
|--------|---------|
| **Ataques DMA** | Deshabilitar puertos de alta velocidad como **Thunderbolt** o **FireWire** si no se usan: pueden ser explotados para leer la RAM directamente |
| **Protección IOMMU** | Habilitar `intel_iommu=on` o `amd_iommu=on` en los parámetros de arranque del kernel para restringir las áreas de memoria a las que los dispositivos físicos pueden acceder |
| **Seguridad de puertos USB** | Bloquear puertos USB no utilizados o usar herramientas como **USBGuard** para permitir únicamente dispositivos conocidos y autorizados |

```text
# Parámetros de arranque en GRUB (GRUB_CMDLINE_LINUX_DEFAULT)
intel_iommu=on
# o para AMD:
amd_iommu=on
```

### 6. Control de Acceso Físico (NIST PE-3)

Desde la perspectiva de **NIST SP 800-53**, la seguridad física no es solo técnica, sino también ambiental.

| Acción | Detalle |
|--------|---------|
| **Restricción de Acceso** | Limitar quién puede estar cerca del equipo físico para evitar la exfiltración de información o la remoción de componentes |
| **Protección contra manipulación** | Usar carcasas con candado o sellos de evidencia de manipulación para detectar si alguien intentó abrir el computador |

> [!note] Resultado de la Fase 2
> Al completar esta fase, habrás establecido una **raíz de confianza** sólida: solo el software autorizado puede ejecutarse y tus datos están protegidos contra el acceso físico no autorizado.

## Fase 3: Hardening del Kernel

La **Fase 3** es uno de los pasos más críticos del proceso: el kernel es el núcleo del sistema operativo y puede considerarse como el binario "setuid root" más grande y vulnerable del sistema. De acuerdo con la **Guía de Hardening de Linux** y los marcos de **NIST** y **CIS**, esta fase implica configurar el kernel para que se proteja a sí mismo y reduzca su superficie de ataque.

### 1. Autoprotección mediante Sysctl

El comando `sysctl` permite configurar parámetros del kernel en tiempo de ejecución para activar funciones de seguridad.

| Parámetro | Valor | Efecto |
|-----------|-------|--------|
| `kernel.kptr_restrict` | `2` | Oculta las direcciones de memoria del kernel, dificultando exploits que dependen de conocer la ubicación de símbolos específicos |
| `kernel.dmesg_restrict` | `1` | Evita que usuarios no privilegiados vean el log del kernel, que puede filtrar información sensible |
| `kernel.unprivileged_bpf_disabled` | `1` | Deshabilita el eBPF no privilegiado (superficie de ataque con abundantes vulnerabilidades) |
| `net.core.bpf_jit_harden` | `2` | Activa el endurecimiento JIT de eBPF |
| `fs.protected_symlinks` | `1` | Protege enlaces simbólicos (previene ataques de condición de carrera / TOCTOU) |
| `fs.protected_hardlinks` | `1` | Protege enlaces duros (misma familia de ataques) |

```bash
# /etc/sysctl.conf
kernel.kptr_restrict = 2
kernel.dmesg_restrict = 1
kernel.unprivileged_bpf_disabled = 1
net.core.bpf_jit_harden = 2
fs.protected_symlinks = 1
fs.protected_hardlinks = 1
net.ipv4.conf.all.rp_filter = 1
net.ipv4.conf.default.rp_filter = 1
```

### 2. Parámetros de Seguridad en el Arranque

Muchos ajustes de seguridad solo pueden aplicarse al inicio del sistema mediante parámetros pasados a través del cargador de arranque (como GRUB).

| Parámetro | Efecto |
|-----------|--------|
| `slab_nomerge` | Deshabilita la fusión de cachés "slab": dificulta ataques de corrupción de memoria al evitar que se sobrescriban objetos desde cachés mezcladas |
| `init_on_alloc=1` | La memoria se llena de ceros al ser asignada (mitiga fuga de datos sensibles) |
| `init_on_free=1` | La memoria se llena de ceros al ser liberada (mitiga "use-after-free" y borra datos de la RAM) |
| `lockdown=confidentiality` | Frontera de seguridad clara entre espacio de usuario y kernel, eliminando métodos de escalada de privilegios |
| `page_alloc.shuffle=1` | Asignaciones de memoria menos predecibles (mejora la seguridad) |
| `pti=on` | Mitiga vulnerabilidades como Meltdown |

```text
# Parámetros de arranque en GRUB (agregar a GRUB_CMDLINE_LINUX_DEFAULT)
slab_nomerge init_on_alloc=1 init_on_free=1 lockdown=confidentiality page_alloc.shuffle=1 pti=on
```

### 3. Reducción de la Superficie de Ataque del Kernel

Consiste en deshabilitar módulos y protocolos que no son estrictamente necesarios para el funcionamiento del equipo.

| Acción | Detalle |
|--------|---------|
| **Lista negra de módulos** | Bloquear protocolos de red oscuros o raramente usados (`dccp`, `sctp`, `rds`, `tipc`) que añaden superficie de ataque remota considerable |
| **Desactivar hardware innecesario** | Si no se usan, deshabilitar módulos de **Bluetooth**, cámaras web (`uvcvideo`) o sistemas de archivos poco comunes (`cifs`, `nfs`, `gfs2`) |
| **Restricción de namespaces** | `kernel.unprivileged_userns_clone=0` restringe los espacios de nombres de usuario a administradores (expone gran superficie para escalada de privilegios) |

```bash
# /etc/modprobe.d/blacklist-hardening.conf
blacklist dccp
blacklist sctp
blacklist rds
blacklist tipc
blacklist uvcvideo
blacklist bluetooth
blacklist cifs
blacklist nfs
blacklist gfs2
```

### 4. Alineación Estratégica (NIST y CIS)

Desde una perspectiva de gobernanza y control de seguridad, estas acciones técnicas se alinean con estándares internacionales:

| Control | Referencia | Qué busca |
|---------|-----------|-----------|
| **Aislamiento de funciones** | NIST SC-3 | Aislar las funciones de seguridad del kernel de las de no seguridad del espacio de usuario, con barreras físicas o lógicas |
| **Integridad de Software** | NIST SI-7 | Firmas digitales para módulos del kernel (`module.sig_enforce=1`) y arranque verificado: solo se ejecuta código autorizado |
| **Configuración Segura** | CIS Control 4 | Evitar configuraciones por defecto (priorizan facilidad de uso sobre seguridad) y aplicar benchmarks o guías de endurecimiento como esta |

> [!warning] Probar antes de aplicar
> Cambios de kernel mal aplicados pueden impedir el arranque. Aplicá con `sysctl -p`, probá un reinicio y mantené la línea base para revertir.

> [!note] Resultado de la Fase 3
> Al finalizar, el kernel de tu Debian 13 no solo será más difícil de comprometer, sino que también tendrá mecanismos para **detectar y fallar de manera segura** ante intentos de explotación.

## Fase 4: Gestión de Identidades, Cuentas y Privilegios

La **Fase 4** se centra en controlar quién puede acceder al sistema y qué acciones está autorizado a realizar, aplicando estrictamente el **principio de menor privilegio**. Basado en la **Guía de Hardening de Linux** y los marcos de **CIS** y **NIST**, las acciones fundamentales son las siguientes.

### 1. Fortalecimiento de la Cuenta Root y Escalada de Privilegios

La cuenta "root" tiene acceso total al sistema y debe ser protegida para evitar que un atacante tome el control completo.

| Acción | Configuración | Detalle |
|--------|---------------|---------|
| **Bloquear la cuenta root** | `passwd -l root` | Bloquea el acceso directo: nadie inicia sesión como root |
| **Restricción de terminales** | Vaciar `/etc/securetty` | Impide inicios de sesión de root desde cualquier terminal |
| **Cuenta administrativa separada** | Crear usuario `admin` + `sudo` | No usar una cuenta normal para tareas administrativas; el admin escala con `sudo` |
| **Restricción de `su`** | Grupo `wheel` (o grupo admin) | Limitar `su` (switch user) únicamente a usuarios autorizados |

```bash
# Bloquear login directo de root
passwd -l root

# Vaciar /etc/securetty (impedir root por terminal)
> /etc/securetty

# Carpeta personal privada
chmod 700 /home/$user
```

### 2. Gestión Robusta de Contraseñas y PAM

El framework **PAM** (Pluggable Authentication Modules) permite configurar políticas de seguridad de alto nivel para la autenticación.

| Acción | Configuración | Efecto |
|--------|---------------|--------|
| **Rondas de hashing** | SHA-512 con 65536 rondas | Descifrar contraseñas por fuerza bruta es mucho más lento y costoso |
| **Calidad de contraseñas** | `pam_pwquality` con longitud mínima 14-16 y complejidad de tipos de caracteres | Exige contraseñas robustas |
| **Retardo ante fallos** | `pam_faildelay` con ~4 segundos | Frena intentos fallidos automatizados de inicio de sesión |

### 3. Autenticación Multifactor (MFA)

NIST y CIS consideran el MFA como un control crítico para proteger las identidades.

| Acción | Detalle |
|--------|---------|
| **MFA para acceso administrativo** | Requerir obligatoriamente un segundo factor de autenticación para todas las cuentas con privilegios administrativos |
| **Tokens físicos** | Para mayor seguridad, uno de los factores debe provenir de un dispositivo físico separado (llave de seguridad o token de hardware) |

### 4. Aplicación del Menor Privilegio en Archivos y Binarios

Esta medida limita el daño potencial que un proceso o usuario comprometido puede causar.

| Acción | Configuración | Efecto |
|--------|---------------|--------|
| **Permisos de directorios personales** | `chmod 700 /home/$user` | En Debian los `/home` pueden ser legibles por otros usuarios; esto lo restringe |
| **Remoción de bits SUID/SGID** | Buscar y eliminar setuid/setgid innecesarios | Evita ejecutar programas con privilegios del dueño (root) — vector común de escalada |
| **Umask** | `umask 0077` en `/etc/profile` | Archivos y carpetas nuevos privados por defecto |

```bash
# Buscar binarios con bits SUID/SGID
find / -perm -4000 -o -perm -2000 2>/dev/null
```

### 5. Control e Inventario de Cuentas (CIS Control 5)

| Acción | Detalle |
|--------|---------|
| **Inventario de cuentas** | Mantener una lista actualizada de todas las cuentas de usuario, administrador y de servicio |
| **Cuentas inactivas** | Deshabilitar o eliminar cuentas sin actividad durante 45 días |
| **Separación de funciones** | Tareas administrativas desde cuentas dedicadas, nunca desde cuentas de uso cotidiano (ej. navegar por internet) |

> [!note] Resultado de la Fase 4
> Al finalizar, habrás establecido una barrera crítica: incluso si un atacante obtiene una contraseña o compromete una aplicación, se encontrará con **múltiples capas de protección** que le impedirán moverse lateralmente o escalar sus privilegios en el sistema.

## Fase 5: Reducción de la Superficie de Ataque (Red y Software)

La **Fase 5** tiene como objetivo minimizar el número de vectores de entrada disponibles para un atacante, limitando las funciones del sistema a lo estrictamente necesario y aislando las aplicaciones vulnerables. Basado en las guías de **NIST**, **CIS** y la **Guía de Hardening de Linux**, las acciones clave son las siguientes.

### 1. Aplicación del Principio de Menor Funcionalidad

Esta medida consiste en configurar el sistema para que proporcione únicamente las capacidades esenciales para su misión.

| Acción | Detalle |
|--------|---------|
| **Eliminación de software y servicios** | Desinstalar o deshabilitar servicios, puertos y protocolos innecesarios (ej. Samba/NFS) y módulos de aplicaciones web no utilizados |
| **Desactivación de componentes del kernel** | Bloquear módulos de protocolos poco comunes (`sctp`, `dccp`) o funciones de hardware como **Bluetooth** y cámaras web (ver Fase 3) |
| **Restricción de software autorizado** | NIST y CIS recomiendan "listas permitidas" (allowlists): solo las aplicaciones y librerías autorizadas pueden ejecutarse |

> [!tip] Regla mental
> Si un servicio no se usa, se desinstala o se apaga. Cada servicio de red activo es una puerta más para un atacante.

### 2. Configuración Defensiva de Red

El control del tráfico entrante y saliente es vital para prevenir la explotación remota y la exfiltración de datos.

| Acción | Detalle |
|--------|---------|
| **Firewall con denegación por defecto** | Implementar firewall basado en el host (`nftables` o `iptables`) con regla de "denegar todo por defecto", permitiendo solo el tráfico de servicios y puertos explícitamente autorizados |
| **Filtrado de DNS y URL** | Usar servicios de filtrado de DNS en el dispositivo para bloquear dominios de malware o usados para comando y control |

```bash
# nftables: política por defecto DROP (denegar todo)
nft add table inet filter
nft chain inet filter input { type filter hook input priority 0 ; policy drop ; }
nft chain inet filter forward { type filter hook forward priority 0 ; policy drop ; }
nft add rule inet filter input ct state established,related accept
# Abrir SOLO el puerto/servicio necesario, ej. SSH:
nft add rule inet filter input tcp dport 22 accept
```

### 3. Aislamiento de Aplicaciones (Sandboxing)

Dado que aplicaciones como los navegadores web son puertas comunes para el malware, deben ejecutarse en entornos aislados.

| Acción | Detalle |
|--------|---------|
| **Uso de Sandboxes** | Herramientas como **bubblewrap** o **gVisor** para aplicaciones riesgosas: barrera que limita el acceso al sistema de archivos y al kernel |
| **Aislamiento de la interfaz gráfica (GUI)** | Preferir **Wayland** sobre Xorg: aísla las ventanas entre sí, evitando keylogging y capturas de pantalla no autorizadas |
| **Prevención de escapes del sandbox** | Restringir desde dentro del sandbox el acceso a comunicación entre procesos (**D-Bus**) y servidores de sonido (**PulseAudio**), vías comunes para evadir el aislamiento |

### 4. Endurecimiento de Software de Usuario

Protección específica para las herramientas que más interactúan con contenido externo.

| Acción | Detalle |
|--------|---------|
| **Seguridad en navegador y correo** | Usar solo software con actualizaciones constantes del proveedor y deshabilitar extensiones/complementos innecesarios o no autorizados |
| **Asignador de memoria endurecido** | Implementar `hardened_malloc`: protecciones sustanciales contra corrupción de memoria en el montón (heap) |
| **Validación de entradas** | Configurar las aplicaciones para validación estricta de todas las entradas de datos, mitigando inyección y desbordamientos |

> [!note] Resultado de la Fase 5
> Al finalizar, habrás transformado tu Debian 13 en un sistema altamente **resistente a la intrusión remota**, donde cada aplicación opera en un entorno controlado y con el mínimo de privilegios y conexiones posibles.

## Fase 6: Monitoreo Continuo, Parcheo y Recuperación

La **Fase 6** garantiza que el nivel de seguridad alcanzado se mantenga en el tiempo y que el sistema sea resiliente ante fallos o ataques. Según los marcos de **NIST** y **CIS**, junto con la **Guía de Hardening de Linux**, esta fase se detalla a continuación.

### 1. Gestión Continua de Vulnerabilidades y Parcheo

El sistema debe actualizarse constantemente para corregir fallos de seguridad (flaws) recién descubiertos.

| Acción | Detalle |
|--------|---------|
| **Remediación de fallos (NIST SI-2)** | Identificar, reportar y corregir los fallos del sistema instalando actualizaciones de seguridad en periodos definidos por la organización |
| **Parcheo automatizado** | CIS recomienda actualizaciones del SO y aplicaciones mediante gestión de parches automatizada de forma mensual o más frecuente |
| **Actualizaciones de Microcódigo** | Instalar actualizaciones de microcode de la CPU (disponibles en los repositorios de Debian) para corregir vulnerabilidades críticas de hardware como Spectre y Meltdown |

```bash
# Actualizaciones de seguridad
apt update && apt upgrade
apt install -y unattended-upgrades
```

### 2. Gestión y Revisión de Logs de Auditoría

La recolección y análisis de logs es vital para detectar actividades maliciosas que de otro modo pasarían desapercibidas.

| Acción | Detalle |
|--------|---------|
| **Recolección de eventos (NIST AU-2)** | Registrar eventos significativos: cambios de contraseñas, accesos fallidos y uso de privilegios administrativos |
| **Centralización y Protección (CIS Control 8)** | Centralizar los logs en un servidor dedicado o SIEM para evitar que un atacante los modifique para ocultar su rastro |
| **Revisión Semanal** | Revisar los logs al menos semanalmente para detectar anomalías o eventos anormales que indiquen una amenaza potencial |

### 3. Recuperación de Datos y Resiliencia

La disponibilidad de los datos es un pilar de la seguridad; el hardening es incompleto si no se pueden recuperar los datos tras un incidente.

| Acción | Detalle |
|--------|---------|
| **Copias de Seguridad Automatizadas** | Respaldos automáticos de la información del usuario y del sistema con frecuencia basada en la sensibilidad de los datos |
| **Protección y Aislamiento de Backups** | Proteger los datos de recuperación con los mismos controles que los originales (cifrado) y mantenerlos en una instancia aislada (fuera de línea o en la nube) |
| **Pruebas de Restauración** | Probar la recuperación de los backups trimestralmente para asegurar que el sistema pueda volver a un estado confiable |

> [!warning] Backup cifrado
> Una práctica de hardening es inútil si perdés tus datos por un fallo o un ataque y no tenés cómo recuperarlos.

### 4. Gestión de Respuesta a Incidentes

Preparar un plan de acción para cuando las protecciones fallen.

| Acción | Detalle |
|--------|---------|
| **Plan de Respuesta (NIST RESPOND)** | Hoja de ruta que incluya detección, análisis, contención, erradicación y recuperación ante incidentes |
| **Lecciones Aprendidas** | Revisiones post-incidente para identificar lecciones que prevengan la recurrencia del evento |

### 5. Monitoreo del Sistema (NIST SI-4)

Implementar una vigilancia activa de las conexiones y el comportamiento del equipo.

| Acción | Detalle |
|--------|---------|
| **Detección de Intrusiones** | Herramientas de monitoreo para detectar ataques, conexiones no autorizadas y el uso indebido del sistema |
| **Alertas de Seguridad** | Alertas automáticas que notifiquen al administrador ante indicadores de compromiso (IOC) o violaciones de seguridad detectadas |

> [!note] Resultado de la Fase 6
> Al concluir esta fase, tu sistema Debian 13 no solo estará "endurecido", sino que contará con un ciclo de **mejora continua**. El hardening se extiende hasta el final de la vida útil del equipo, adaptándose a nuevas amenazas y asegurando que la configuración de seguridad no se degrade con el tiempo.

## Puntos críticos y Deep Dives

De acuerdo con las fuentes proporcionadas (NIST, CIS y la Guía de Hardening de Linux), existen puntos críticos donde no basta con aplicar una configuración: se debe **indagar profundamente en los riesgos y los impactos operativos**.

### 1. El Dilema entre Usabilidad y Seguridad (Trade-off)

Observación transversal a todo el proceso. **NIST SP 800-53** advierte que existe un equilibrio delicado entre la facilidad de uso y la rigurosidad de las políticas.

> [!warning] Trade-off
> Si las protecciones son demasiado frustrantes, los usuarios buscarán formas de evitarlas o deshabilitarlas. Al endurecer Debian 13, evaluá qué medidas (bloqueo estricto de puertos, *sandboxing*) podrían impedir que realices tus tareas diarias de forma eficiente.

### 2. La Limitación Crítica del Cifrado de Disco (Fase 2)

Aunque el cifrado completo de disco (LUKS) protege los datos en reposo, tiene un "agujero" importante.

> [!warning] Deep Dive: /boot sin cifrar
> El cifrado estándar **no suele cubrir la partición `/boot`**, lo que permite que un atacante físico modifique el kernel o el gestor de arranque antes de que el sistema se cifre. Para mitigarlo, indagá en la implementación de **Verified Boot** (Arranque Verificado), que asegura criptográficamente que ningún componente haya sido alterado antes de ejecutarse.

### 3. La Selección del Kernel: Estabilidad vs. Seguridad (Fase 3)

No todos los kernels son iguales en términos de endurecimiento.

| Opción | Ventaja | Riesgo |
|--------|---------|--------|
| **Kernel LTS** | Soporte prolongado y estabilidad | Parches de seguridad más atrasados |
| **Kernel estable** | Parches de seguridad más recientes | Nuevas funciones que aumentan la superficie de ataque |
| **Kernel compilado a medida** | Desactiva módulos innecesarios: reduce drásticamente lo explotable | Mayor mantenimiento y complejidad |

> [!note] Indagación
> Investigá si para tu práctica conviene el kernel estándar de Debian o compilar uno propio para desactivar módulos innecesarios.

### 4. Escapes de Sandbox y Aislamiento de GUI (Fase 5)

Ejecutar aplicaciones en contenedores o *sandboxes* no es una solución mágica.

> [!warning] Deep Dive
> **PulseAudio** y **D-Bus** son vías frecuentes para "escapar" de un entorno aislado. En el entorno gráfico, **Xorg** es intrínsecamente inseguro: cualquier ventana puede registrar lo que escribís en otra (keylogging). Las fuentes recomiendan migrar a **Wayland**, que aísla las ventanas por defecto.

### 5. La "Ceguera" de los Logs y el Monitoreo (Fase 6)

Recolectar logs no sirve de nada si no se analizan correctamente.

> [!warning] Deep Dive
> CIS enfatiza que recolectar logs es solo una parte; la clave es la **correlación de eventos**. NIST sugiere análisis de tendencias y de texto completo en comandos privilegiados para detectar comportamientos anómalos que el software automático podría ignorar. Investigá cómo Debian registra las actividades de `sudo` y cómo automatizar alertas ante fallos repetidos.

### 6. La Seguridad como Problema de Diseño (Fase 1)

La seguridad no es una capa que se añade al final, sino un problema de diseño.

> [!note] Observación
> Antes de ejecutar comandos, la parte más ignorada es el **Inventario de Activos y Clasificación de Datos** (CIS Controles 1, 2 y 3). Si no sabés qué software tenés o qué datos son realmente sensibles, aplicarás el mismo nivel de esfuerzo a cosas triviales que a cosas críticas, desperdiciando recursos.

> [!tip] Prioridad de indagación
> Profundizá sobre todo en la **seguridad del arranque (Verified Boot)** y en las **limitaciones de Xorg frente a Wayland**: son los eslabones más débiles una vez que el sistema operativo está funcionando.

## ¿Hasta dónde se extiende el hardening?

El hardening es un proceso que **no tiene un final definitivo**. Se extiende desde el diseño inicial del sistema hasta su desmantelamiento, adaptándose constantemente a medida que:

1. Aparecen nuevas amenazas o tipos de ataques.
2. Instalás nuevo software que introduce nuevas vulnerabilidades.
3. Tus necesidades personales o de trabajo cambian, requiriendo un ajuste entre seguridad y usabilidad.

> [!tip] Por dónde empezar
> Como principiante, enfocate primero en la **higiene esencial (IG1)** de CIS: actualizaciones, contraseñas fuertes, cortafuegos y copias de seguridad. A medida que ganes confianza, profundizá en configuraciones avanzadas del kernel y políticas de control de acceso mandatorio (como AppArmor).

## Checklist básico (IG1)

- [ ] Inventario de activos documentado
- [ ] Datos sensibles identificados
- [ ] Línea base de configuración guardada
- [ ] BIOS/UEFI con contraseña y sin arranque USB
- [ ] Disco cifrado con LUKS
- [ ] GRUB protegido con contraseña
- [ ] Sysctl con restricciones de kernel activas
- [ ] Root bloqueado, uso de `sudo`
- [ ] PAM con políticas de contraseña fuertes
- [ ] Servicios innecesarios eliminados
- [ ] Firewall `nftables` con denegación por defecto
- [ ] `umask 0077` en `/etc/profile`
- [ ] Actualizaciones de seguridad automáticas
- [ ] Logs configurados y revisados
- [ ] Backups automáticos y cifrados

## Notas personales
- 

## Referencia
- [[40 - RECURSOS/MOCs/MOC - Ciberseguridad]]
- [[MOC - DevSecOps]]

#ciberseguridad #hardening #linux #debian #nist #cis #sysadmin #kernel #firewall
