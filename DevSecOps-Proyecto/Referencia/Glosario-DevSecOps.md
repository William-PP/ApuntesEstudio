# Glosario DevSecOps

## A

**Alerta (Alert)**
Notificación automática cuando se cumple una condición (ej: error rate > 5%).

**AML (Abuse Case Model)**
Modelo que describe cómo un atacante puede abusar del sistema.

---

## B

**Backdoor**
Puerta trasera en el código que permite acceso no autorizado.

**Build**
Proceso de compilar el código fuente en un ejecutable o imagen.

---

## C

**CORS (Cross-Origin Resource Sharing)**
Mecanismo que controla qué dominios pueden acceder a los recursos de tu API.

**Container**
Instancia corriendo de una imagen Docker. Aísla la app del sistema host.

**Coverage (Code Coverage)**
Porcentaje del código fuente que está cubierto por tests.

**CVE (Common Vulnerabilities and Exposures)**
Base de datos pública de vulnerabilidades de seguridad conocidas.

---

## D

**DAST (Dynamic Application Security Testing)**
Pruebas de seguridad contra la aplicación corriendo (simula ataques HTTP).

**DDoS (Distributed Denial of Service)**
Ataque de denegación de servicio distribuido.

**Dependabot**
Herramienta de GitHub que detecta dependencias con vulnerabilidades y crea PRs para actualizarlas.

---

## E

**EKS (Elastic Kubernetes Service)**
Servicio gestionado de Kubernetes de AWS.

**Endpoint**
Punto de acceso de una API (ej: `GET /api/team`).

---

## G

**Gitleaks**
Herramienta para detectar secretos (API keys, passwords) commiteados en Git.

---

## H

**Health Check**
Endpoint que reporta el estado de la aplicación y sus dependencias.

---

## I

**IaC (Infrastructure as Code)**
Definir infraestructura en archivos de código versionado.

**Immutable Infrastructure**
Infraestructura que no se modifica después de desplegada — se reemplaza con una nueva versión.

---

## J

**JWT (JSON Web Token)**
Estándar para transmitir información de forma segura entre partes.

---

## K

**Kubernetes (K8s)**
Plataforma de orquestación de contenedores.

---

## L

**Liveness Probe**
Check que verifica si el container está vivo. Si falla, Kubernetes lo reinicia.

---

## M

**MITM (Man-in-the-Middle)**
Ataque donde el atacante intercepta comunicación entre dos partes.

---

## N

**Non-root**
Ejecutar un container como usuario sin privilegios de root.

---

## O

**OPA (Open Policy Agent)**
Motor de políticas para validar configuraciones de infraestructura.

---

## P

**Penetration Testing (Pentest)**
Prueba de autorizada de seguridad que simula un ataque real.

**Pod**
Unidad más pequeña en Kubernetes — puede contener uno o más containers.

**Policy as Code**
Definir reglas de seguridad como código que se aplica automáticamente.

**Prometheus**
Herramienta de monitoreo y recolección de métricas.

---

## Q

**Quality Gate**
Condición que debe cumplirse para que el pipeline continue (ej: coverage > 70%).

---

## R

**Rate Limiting**
Limitar cuántas peticiones puede hacer un usuario en un período de tiempo.

**RBAC (Role-Based Access Control)**
Control de acceso basado en roles asignados a usuarios.

**Readiness Probe**
Check que verifica si el container está listo para recibir tráfico.

**Repudiation**
Cuando un usuario puede negar haber realizado una acción (no hay logs de evidencia).

---

## S

**SAST (Static Application Security Testing)**
Análisis estático del código fuente para encontrar vulnerabilidades.

**SCA (Software Composition Analysis)**
Escaneo de dependencias/librerías por vulnerabilidades conocidas.

**SCM (Source Code Management)**
Sistema de control de versiones (Git).

**Sealed Secrets**
Secretos encriptados que pueden guardarse de forma segura en Git.

**Secret Scanning**
Detectar secretos accidentalmente commiteados al repositorio.

**Security Context**
Configuración de seguridad a nivel de Pod/Container en Kubernetes.

**Shift Left**
Mover la práctica de seguridad lo más temprano posible en el ciclo de desarrollo.

**SOC (Security Operations Center)**
Equipo que monitorea y responde a incidentes de seguridad.

**STRIDE**
Modelo de amenazas: Spoofing, Tampering, Repudiation, Info Disclosure, DoS, Elevation of Privilege.

---

## T

**Threat Modeling**
Proceso para identificar y evaluar amenazas de seguridad.

**Trivy**
Herramienta de escaneo de vulnerabilidades en imágenes Docker y IaC.

---

## V

**Vault (HashiCorp Vault)**
Herramienta para gestionar secrets en producción.

**Vulnerability**
Debilidad en el sistema que puede ser explotada por un atacante.

---

## W

**Workflow**
Archivo YAML que define un pipeline automatizado en GitHub Actions.

---

## Y

**Zero Trust**
Modelo de seguridad que nunca confía, siempre verifica — ni siquiera dentro de la red.

## Links Relacionados
- [[00-Visión-General]]

## Referencia
- [[DevSecOps-Proyecto/00-Visión-General]]
- [[DevSecOps-Proyecto/Referencia/Comandos-Útiles]]
- [[DevSecOps-Proyecto/Referencia/Vulnerabilidades-Encontradas]]

#config #devsecops #containers #cloud #database #proyecto #api #git #logging #vulnerabilidades #secrets #testing #referencia #monitoring
