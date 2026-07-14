# DAST — Dynamic Application Security Testing (Detallado)

## QUÉ es

Análisis de la aplicación **mientras está corriendo** (en runtime), enviando requests HTTP manipulados para detectar vulnerabilidades.

```
DAST Scanner → HTTP requests maliciosos → App corriendo → Respuesta → Análisis
```

## Diferencia con SAST

| Aspecto | SAST | DAST |
|---------|------|------|
| Analiza | Código fuente | App corriendo |
| Ejecución | No necesita correr la app | Necesita la app corriendo |
| Tipo de漏洞 | Code-level | Runtime/Config |
| Velocidad | Rápido | Más lento (HTTP requests) |
| Falsos positivos | Más frecuentes | Menos frecuentes |

## QUÉ detecta

| Vulnerabilidad | Cómo lo detecta |
|---------------|-----------------|
| **SQL Injection** | Inyecta payloads SQL y analiza respuestas |
| **XSS** | Inyecta scripts y verifica si se ejecutan |
| **CSRF** | Verifica si faltan protecciones |
| **Authentication flaws** | Prueba bypass de login |
| **Misconfigurations** | Headers faltantes, debug mode activo |
| **Information disclosure** | Errores que revelan info del servidor |

## CÓMO funciona

```
1. Crawling (arañado)
   Scanner descubre endpoints, formularios, parámetros
         │
         ▼
2. Fuzzing
   Envía payloads maliciosos a cada endpoint
   " OR 1=1 --
   <script>alert('xss')</script>
   ../../../etc/passwd
         │
         ▼
3. Análisis de respuestas
   ¿La respuesta indica vulnerabilidad?
   ¿Retorna datos que no debería?
   ¿Muestra error detallado?
         │
         ▼
4. Reporte
   Lista de vulnerabilidades con evidencia
```

## Herramientas principales

### OWASP ZAP (Zed Attack Proxy)

**Gratis, open source, el más usado para DAST**

```bash
# Docker
docker run -t ghcr.io/zaproxy/zaproxy:stable zap-baseline.py \
  -t https://tu-app.com

# GitHub Actions
- name: OWASP ZAP Scan
  uses: zaproxy/action-baseline@v0.10.0
  with:
    target: 'https://tu-app.com'
```

### Burp Suite

**La herramienta profesional de pentesting**

| Versión | Costo | Uso |
|---------|-------|-----|
| Community | Gratis | Básico, manual |
| Professional | ~$449/año | Automatizado, avanzado |

## CUÁNDO ejecutar

| Momento | Cuándo |
|---------|--------|
| Desarrollo | Manual, contra localhost |
| CI/CD (pr) | Opcional (lento) |
| Pre-producción | ✅ Obligatorio |
| Producción | Periodicamente |

## Limitaciones

| Limitación | Solución |
|-----------|---------|
| No ve el código fuente | Complementar con SAST |
| Solo ve lo que está corriendo | Asegurar que el entorno de test sea representativo |
| Puede ser lento | Ejecutar en paralelo o en pipeline separado |
| Falsos positivos en configs | Revisar manualmente |

## Referencia
- [[DevSecOps-Estudio/00-Index]]
- [[Introduccion-AppSec-DevSecOps]]
- [[CI-CD/GitHub-Actions]]
