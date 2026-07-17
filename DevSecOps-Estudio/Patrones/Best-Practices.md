# Best Practices — DevSecOps

> [!info] Cómo usar esta nota
> Cada sección es un checklist accionable. Márcalo con `- [x]` en Obsidian a medida que lo implementes en un proyecto real. Piensa en esto como el "definition of done" de seguridad antes de llevar algo a producción.
> Al final de cada bloque hay una tabla con el **porqué** de cada práctica.

## Código

- [ ] Input validation en todos los endpoints
- [ ] Parameterized queries (nunca concatenar strings)
- [ ] Autenticación en todos los endpoints (excepto health checks)
- [ ] Autorización por roles
- [ ] Rate limiting en endpoints sensibles
- [ ] Structured logging (Serilog)
- [ ] Health checks configurados
- [ ] Secrets fuera del código (User Secrets / env vars)
- [ ] HTTPS habilitado
- [ ] CORS configurado con origins específicos
- [ ] Excepciones manejadas (no exponer stack traces al cliente)

### Por qué

|Práctica|Por qué importa|Qué pasa si no lo haces|
|---|---|---|
|Input validation|Toda entrada externa es potencialmente hostil; validar temprano evita que datos malformados lleguen a capas más profundas|Injection, crashes, lógica de negocio corrompida|
|Parameterized queries|Separa el código SQL de los datos del usuario — el motor nunca interpreta el input como comandos|SQL Injection (A05:2025) — acceso o borrado no autorizado de datos|
|Autenticación en endpoints|Sin verificar identidad, cualquiera con la URL accede a los datos|Exposición total de la API a internet|
|Autorización por roles|Autenticarse solo confirma _quién eres_, no _qué puedes hacer_ — son dos controles distintos|Un usuario normal ejecuta acciones de Admin (Broken Access Control, A01:2025)|
|Rate limiting|Sin límite, un atacante puede probar miles de contraseñas o saturar el endpoint|Brute force exitoso, Denial of Service|
|Structured logging|Logs en formato JSON/clave-valor son buscables y correlacionables entre servicios|Logs de texto libre imposibles de analizar en un incidente real|
|Health checks|Kubernetes/el balanceador necesita saber si la app está viva antes de enviarle tráfico|Tráfico dirigido a instancias caídas o en mal estado|
|Secrets fuera del código|Si el secret está en el repo, queda en el historial de Git para siempre, aunque lo borres después|Credenciales filtradas si el repo se hace público o se comparte|
|HTTPS habilitado|Sin cifrado, cualquiera en la misma red puede leer el tráfico (contraseñas, tokens)|Man-in-the-middle, robo de sesión|
|CORS con origins específicos|`*` permite que cualquier sitio web haga peticiones a tu API desde el navegador de un usuario logueado|CSRF-like: sitios maliciosos consumiendo tu API con la sesión de la víctima|
|Excepciones manejadas|Un exception no controlado puede filtrar información sensible (stack trace, rutas internas, versiones de librerías)|Stack traces expuestos al cliente revelan arquitectura interna y posibles puntos de entrada (Security Misconfiguration, A02:2025)|

## Testing

- [ ] Unit tests para services (>70% coverage)
- [ ] Integration tests para controllers
- [ ] Security tests (auth, authorization, input validation)
- [ ] Tests ejecutados en CI pipeline

### Por qué

|Práctica|Por qué importa|Qué pasa si no lo haces|
|---|---|---|
|Unit tests >70%|Cubre la lógica de negocio aislada; el % es un proxy razonable de "lo esencial está probado"|Regresiones silenciosas en cada cambio de código|
|Integration tests|Prueban que las piezas (controller + service + DB) funcionan juntas, no solo por separado|Bugs que solo aparecen en la integración real, no en mocks|
|Security tests|Prueban explícitamente que un usuario sin permiso NO puede hacer algo — es un test que espera un fallo controlado|Un cambio futuro rompe la autorización y nadie se entera hasta producción|
|Tests en CI|Si no corren automáticamente, dependen de que alguien se acuerde de correrlos a mano|Código roto o inseguro llega a `main`|

## Docker

- [ ] Multi-stage build
- [ ] Imagen base mínima (aspnet runtime, no SDK)
- [ ] Usuario no-root
- [ ] .dockerignore configurado
- [ ] No secrets en la imagen
- [ ] Health check en Dockerfile
- [ ] Trivy scan sin Critical/High
- [ ] Imágenes firmadas (Docker Content Trust / Cosign)

### Por qué

|Práctica|Por qué importa|Qué pasa si no lo haces|
|---|---|---|
|Multi-stage build|Separa las herramientas de compilación (SDK, compiladores) de lo que realmente corre en producción|Imagen final pesada y con herramientas que un atacante podría usar si compromete el contenedor|
|Imagen base mínima|Menos paquetes = menos superficie de ataque (menos CVEs posibles)|El SDK completo trae compiladores y utilidades innecesarias en runtime, cada una con sus propias vulnerabilidades|
|Usuario no-root|Si el proceso corre como root y el contenedor se compromete, el atacante tiene privilegios root dentro del contenedor|Container breakout más fácil, mayor impacto de cualquier vulnerabilidad explotada|
|`.dockerignore`|Evita copiar archivos innecesarios o sensibles (`.git`, `.env`, `bin/`) a la imagen|Secrets o historial de Git terminan empaquetados dentro de la imagen|
|No secrets en la imagen|Las capas de Docker persisten aunque borres el archivo en una capa posterior — el secret sigue ahí|Cualquiera con acceso a la imagen (`docker history`, registry) extrae el secret|
|Health check en Dockerfile|Permite que el orquestador (Docker/K8s) detecte automáticamente un contenedor colgado|Contenedores "zombis" que responden pero no funcionan correctamente siguen recibiendo tráfico|
|Trivy scan sin Critical/High|Detecta CVEs conocidas en el SO base y librerías antes de desplegar|Vulnerabilidades conocidas y ya parcheadas en versiones nuevas llegan a producción sin necesidad|
|Imágenes firmadas|Garantiza que la imagen que despliegas es exactamente la que salió del pipeline, sin manipulación intermedia|Un atacante podría reemplazar imágenes en el registry si no hay verificación de integridad (Software or Data Integrity Failures, A08:2025)|

## CI/CD

- [ ] Pipeline automatizado en cada push/PR
- [ ] Build + Test como mínimo
- [ ] SAST (análisis estático)
- [ ] SCA (escaneo de dependencias)
- [ ] Secret scanning (Gitleaks)
- [ ] Quality gates (pipeline falla si hay issues)
- [ ] Dependabot configurado

### Por qué

|Práctica|Por qué importa|Qué pasa si no lo haces|
|---|---|---|
|Pipeline en cada push/PR|Detecta problemas antes de hacer merge, no después (Shift-Left)|Bugs y vulnerabilidades se descubren ya en `main` o en producción|
|Build + Test mínimo|Garantiza que lo que se va a desplegar al menos compila y pasa las pruebas existentes|Código que ni siquiera compila llega a revisión humana|
|SAST|Encuentra patrones inseguros en el código fuente sin necesidad de ejecutarlo|Vulnerabilidades de código (SQLi, XSS) pasan desapercibidas hasta un pentest o un incidente|
|SCA|La mayoría de vulnerabilidades en apps modernas vienen de dependencias de terceros, no de código propio|Se usa una librería con un CVE conocido sin que nadie se entere|
|Secret scanning (Gitleaks)|Detecta si alguien commiteó una API key o contraseña por error, antes de que llegue al repo remoto/PR|Credenciales expuestas públicamente, a veces indexadas por bots en minutos|
|Quality gates|Sin esto, un SAST/SCA que reporta problemas es solo informativo — nadie está obligado a corregirlos|Los hallazgos de seguridad se acumulan y nunca se resuelven|
|Dependabot|Las dependencias vulnerables se descubren constantemente; hay que actualizar de forma continua, no una vez al año|El proyecto acumula deuda de seguridad silenciosa con el tiempo|

## Kubernetes

- [ ] Security context (runAsNonRoot, readOnlyRootFilesystem)
- [ ] Network policies
- [ ] RBAC configurado
- [ ] Secrets encriptados (Sealed Secrets)
- [ ] Resource limits en pods
- [ ] Health probes configuradas

### Por qué

|Práctica|Por qué importa|Qué pasa si no lo haces|
|---|---|---|
|Security context|Refuerza a nivel de K8s lo mismo que el usuario no-root en Docker, y además bloquea escritura al filesystem del contenedor|Un contenedor comprometido puede modificar sus propios archivos o escalar privilegios más fácilmente|
|Network policies|Por defecto, en K8s todos los pods pueden hablar con todos — sin política, no hay segmentación|Un pod comprometido puede moverse lateralmente y alcanzar cualquier otro servicio del clúster|
|RBAC|Cada componente (service account, usuario) debería poder hacer solo lo que necesita, ni más|Un pod o pipeline comprometido con permisos de cluster-admin puede controlar todo el clúster|
|Secrets encriptados|Los K8s Secrets nativos solo están codificados en base64, no cifrados — cualquiera con acceso al manifiesto los lee|Secrets expuestos si el YAML se versiona en Git o alguien accede al etcd|
|Resource limits|Sin límites, un pod con un bug (o un ataque) puede consumir toda la CPU/memoria del nodo|Un solo pod problemático tumba a los demás pods del mismo nodo (noisy neighbor / DoS)|
|Health probes|`livenessProbe` reinicia pods colgados; `readinessProbe` evita enviarles tráfico hasta que estén listos|Tráfico enviado a pods que no pueden procesarlo, o pods colgados que nunca se reinician solos|

## Monitoreo

- [ ] Structured logging habilitado
- [ ] Métricas HTTP (requests, duration, errors)
- [ ] Dashboard en Grafana
- [ ] Alertas configuradas
- [ ] Security logging (login fallidos, rate limits)
- [ ] Retención de logs definida

### Por qué

|Práctica|Por qué importa|Qué pasa si no lo haces|
|---|---|---|
|Structured logging|Necesario para poder buscar y correlacionar eventos entre múltiples servicios/instancias|Investigar un incidente se vuelve buscar a mano en archivos de texto plano|
|Métricas HTTP|Permiten detectar degradación de performance o picos de errores antes de que el usuario se queje|Los problemas se descubren solo cuando alguien reporta que "la app está lenta"|
|Dashboard en Grafana|Da visibilidad centralizada del estado del sistema en tiempo real|Nadie tiene una vista completa de la salud del sistema hasta que algo falla|
|Alertas configuradas|Sin alertas, alguien tiene que estar mirando el dashboard activamente para notar un problema|Incidentes se detectan tarde, aumentando el tiempo de exposición/downtime|
|Security logging|Login fallidos repetidos o rate limits alcanzados son señales tempranas de un ataque en curso|Un ataque de fuerza bruta o scraping pasa completamente desapercibido|
|Retención de logs|Sin logs históricos no hay forma de reconstruir qué pasó en una investigación forense|Imposible auditar un incidente descubierto días o semanas después de que ocurrió|

---

## Referencia rápida — mapeo a OWASP Top 10:2025

|Categoría OWASP|Dónde se cubre en este checklist|
|---|---|
|A01 Broken Access Control|Autorización por roles, RBAC en K8s|
|A02 Security Misconfiguration|CORS, imagen base mínima, security context, excepciones manejadas|
|A03 Software Supply Chain Failures|SCA, Dependabot, Trivy scan|
|A04 Cryptographic Failures|HTTPS/HSTS, secrets fuera del código|
|A05 Injection|Parameterized queries, input validation|
|A06 Vulnerable and Outdated Components|SCA, Dependabot, Trivy scan|
|A07 Authentication Failures|Autenticación en endpoints, rate limiting|
|A08 Software or Data Integrity Failures|Imágenes firmadas, health checks en Dockerfile|
|A09 Logging and Alerting Failures|Structured/security logging, alertas|
|A10 Mishandling of Exceptional Conditions|Excepciones manejadas, health checks|

---

#devsecops #checklist #ciclo-de-vida-software #docker #kubernetes #cicd
### Referencia
- [[DevSecOps-Estudio/00-Index]]
- [[Patrones/Anti-Patrones-Seguridad]]
