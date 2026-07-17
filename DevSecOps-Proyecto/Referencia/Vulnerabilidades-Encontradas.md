# Vulnerabilidades Encontradas

## Registro de issues de seguridad encontrados

> Documenta cada vulnerabilidad que encuentres durante el desarrollo. Útil para aprender y no repetir errores.

---

## Plantilla

### # [Número] — [Título]
- **Fecha:** 
- **Fase encontrada:** 
- **Herramienta:** (Trivy, Gitleaks, SonarQube, etc.)
- **Severidad:** Critical / High / Medium / Low
- **Archivo/Componente:** 
- **Descripción:** 
- **CVE (si aplica):** 
- **Cómo se arregló:** 
- **Tiempo de arreglo:** 
- **Lección aprendida:** 

---

## Ejemplo

### #1 — Connection string hardcodeada en appsettings.json
- **Fecha:** 2026-07-14
- **Fase encontrada:** Fase -1 (Diseño Seguro)
- **Herramienta:** Revisión manual
- **Severidad:** High
- **Archivo/Componente:** SportsLeague.API/appsettings.json
- **Descripción:** La connection string estaba en texto plano en el archivo de configuración, commiteado al repo.
- **CVE:** N/A
- **Cómo se arregló:** Se movió a User Secrets en desarrollo y Environment Variables en producción.
- **Tiempo de arreglo:** 15 minutos
- **Lección aprendida:** Siempre usar User Secrets desde el inicio del proyecto.

---

<!-- Agrega tus vulnerabilidades aquí usando la plantilla -->

## Referencia
- [[DevSecOps-Proyecto/00-Visión-General]]
- [[DevSecOps-Proyecto/Referencia/Glosario-DevSecOps]]
- [[DevSecOps-Proyecto/Referencia/Comandos-Útiles]]

#config #devsecops #proyecto #api #git #vulnerabilidades #secrets #referencia
