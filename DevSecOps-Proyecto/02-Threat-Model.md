# Threat Model

## ¿Qué es Threat Modeling?
Proceso sistemático para identificar, cuantificar y abordar las amenazas de seguridad de un sistema. Se usa el framework **STRIDE**.

## Datos que Maneja la Aplicación

| Dato | Sensible? | Requiere encryptación en tránsito | Requiere encryptación en reposo |
|------|-----------|-----------------------------------|----------------------------------|
| | | | |

## Análisis STRIDE

### S - Spoofing (Suplantación de identidad)
> *¿Quién puede hacerse pasar por otro usuario?*

| # | Escenario | Riesgo | Mitigación | Estado |
|---|-----------|--------|------------|--------|
| 1 | | | | Pendiente |

### T - Tampering (Manipulación de datos)
> *¿Puede alguien modificar datos que no debiera?*

| # | Escenario | Riesgo | Mitigación | Estado |
|---|-----------|--------|------------|--------|
| 1 | | | | Pendiente |

### R - Repudiation (Repudio)
> *¿Puedo rastrear quién hizo qué?*

| # | Escenario | Riesgo | Mitigación | Estado |
|---|-----------|--------|------------|--------|
| 1 | | | | Pendiente |

### I - Info Disclosure (Divulgación de información)
> *¿Pueden leer datos sensibles?*

| # | Escenario | Riesgo | Mitigación | Estado |
|---|-----------|--------|------------|--------|
| 1 | | | | Pendiente |

### D - Denial of Service (Denegación de servicio)
> *¿Pueden tumbar la aplicación?*

| # | Escenario | Riesgo | Mitigación | Estado |
|---|-----------|--------|------------|--------|
| 1 | | | | Pendiente |

### E - Elevation of Privilege (Elevación de privilegios)
> *¿Pueden hacer más de lo que deben?*

| # | Escenario | Riesgo | Mitigación | Estado |
|---|-----------|--------|------------|--------|
| 1 | | | | Pendiente |

## Superficie de Ataque

### Endpoints expuestos
<!-- Lista los endpoints de tu API -->
| Método | Ruta | Autenticado? | Rate Limited? |
|--------|------|-------------|---------------|
| | | | |

### Datos en tránsito
- Protocolo: HTTP / HTTPS
- TLS version: 

### Datos en reposo
- BD encryptada: Sí / No
- Secrets en: User Secrets / Env vars / Vault

### Autenticación
- Mecanismo: 
- Token expiry: 
- Refresh tokens: Sí / No

## Matriz de Riesgos

| # | Amenaza | Probabilidad | Impacto | Riesgo | Mitigación |
|---|---------|-------------|---------|--------|------------|
| 1 | | Baja/Media/Alta | Bajo/Medio/Alto | | |

## Plan de Mitigación
<!-- Prioriza por riesgo más alto -->

1. **Prioridad Alta:**
2. **Prioridad Media:**
3. **Prioridad Baja:**

## Links Relacionados
- [[00-Visión-General]]
- [[01-Arquitectura]]
- [[03-Decisiones-Técnicas]]

## Referencia
- [[DevSecOps-Proyecto/00-Visión-General]]

#secrets #proyecto #api #devsecops
