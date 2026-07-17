# Grafana — Dashboards

## QUÉ es

Plataforma de **visualización de datos** que crea dashboards interactivos a partir de métricas de Prometheus, logs, y otras fuentes.

## POR QUÉ usarlo

| Sin Grafana | Con Grafana |
|-------------|-------------|
| Logs en texto | Dashboards visuales |
| Sin alertas visuales | Alertas configurables |
| Manual para revisar métricas | Todo en un solo lugar |

## Dashboard típico de DevSecOps

### Panel de la API
- Requests por segundo
- Error rate (4xx, 5xx)
- Latencia (p50, p95, p99)
- Uptime

### Panel de Seguridad
- Login fallidos por minuto
- Rate limit hits
- Tokens expirados
- Requests bloqueados por CORS

### Panel de Infraestructura
- CPU usage
- Memory usage
- Disk usage
- Network I/O

## CÓMO configurar

```yaml
# docker-compose.yml
services:
  grafana:
    image: grafana/grafana
    ports:
      - "3000:3000"
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=admin
    volumes:
      - grafana-data:/var/lib/grafana
```

## DÓNDE acceder

- URL: `http://localhost:3000`
- Usuario: `admin`
- Password: `admin` (cambiar en producción)

## Referencia
- [[DevSecOps-Estudio/00-Index]]

#config #devsecops #api #estudio #logging #grafana #dashboard #monitoreo #monitoring
