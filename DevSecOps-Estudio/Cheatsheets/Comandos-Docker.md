# Cheatsheet — Docker

## Comandos esenciales

```bash
# ═══════════════════════════════════════════════════
# IMÁGENES
# ═══════════════════════════════════════════════════
docker build -t name:tag .          # Construir imagen
docker images                       # Ver imágenes
docker rmi image-id                 # Eliminar imagen
docker image prune -a               # Limpiar imágenes no usadas

# ═══════════════════════════════════════════════════
# CONTAINERS
# ═══════════════════════════════════════════════════
docker run -p 8080:8080 name:tag    # Ejecutar
docker run -d name:tag              # Detached (background)
docker run --rm name:tag            # Eliminar al salir
docker run -it name:tag /bin/bash   # Interactivo
docker ps                           # Containers corriendo
docker ps -a                        # Todos los containers
docker stop container-id            # Parar
docker rm container-id              # Eliminar

# ═══════════════════════════════════════════════════
# DEBUGGING
# ═══════════════════════════════════════════════════
docker logs container-id            # Ver logs
docker logs -f container-id         # Seguimiento en tiempo real
docker exec -it container-id /bin/bash  # Entrar al container
docker inspect container-id         # Info detallada
docker stats                        # Uso de recursos

# ═══════════════════════════════════════════════════
# COMPOSE
# ═══════════════════════════════════════════════════
docker-compose up -d                # Iniciar
docker-compose down                 # Parar
docker-compose ps                   # Ver servicios
docker-compose logs -f              # Logs
docker-compose up --build           # Reconstruir

# ═══════════════════════════════════════════════════
# SEGURIDAD
# ═══════════════════════════════════════════════════
trivy image name:tag                # Escanear imagen
trivy image --severity CRITICAL,HIGH name:tag
docker system prune -a              # Limpiar todo
```

## Referencia
- [[00-Index]]
- [[Docker/Fundamentos]]
