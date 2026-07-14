# Troubleshooting — Errores Comunes

## .NET

### 401 Unauthorized
**Causa:** Token no enviado o inválido
**Solución:**
- Verificar que el header `Authorization: Bearer <token>` esté presente
- Verificar que el token no esté expirado
- Verificar que `UseAuthentication()` está antes de `UseAuthorization()` en Program.cs

### 403 Forbidden
**Causa:** Token válido pero sin permisos
**Solución:**
- Verificar que el usuario tiene el rol correcto
- Verificar que el endpoint tiene `[Authorize(Roles = "...")]` correcto

### CORS error en navegador
**Causa:** Origin no está en la lista de CORS
**Solución:**
- Verificar que el origin del frontend está en `WithOrigins()`
- Verificar que `UseCors()` está antes de `MapControllers()`

### NullReferenceException en config
**Causa:** Secret no encontrado en configuration
**Solución:**
- Verificar User Secrets: `dotnet user-secrets list`
- Verificar Environment Variables
- Verificar que el key en config coincide con el secret

### Connection refused (SQL Server)
**Causa:** SQL Server no está corriendo o connection string incorrecto
**Solución:**
- Verificar que SQL Server está corriendo
- Verificar el connection string en User Secrets
- Verificar que `TrustServerCertificate=true` está en el string

---

## Docker

### Permission denied
**Causa:** Container corriendo como root pero archivos con otro owner
**Solución:**
```dockerfile
RUN chown -R appuser:appgroup /app
USER appuser
```

### Imagen muy grande
**Causa:** No usa multi-stage build
**Solución:** Usar stage de build + stage de runtime

### Container se reinicia
**Causa:** Proceso principal falla
**Solución:**
- Verificar logs: `docker logs container-id`
- Verificar que el ENTRYPOINT es correcto
- Verificar que dependencias están disponibles

---

## Kubernetes

### Pod en CrashLoopBackOff
**Causa:** Container falla al iniciar
**Solución:**
```bash
kubectl describe pod pod-name
kubectl logs pod-name --previous
```
- Verificar image name y tag
- Verificar que el proceso no falla
- Verificar variables de entorno

### Pod en Pending
**Causa:** No hay recursos suficientes o restricciones
**Solución:**
```bash
kubectl describe pod pod-name
```
- Verificar eventos (Events section)
- Verificar resource requests/limits
- Verificar node selectors/affinity

### Service no responde
**Causa:** Selector no coincide con pods o puerto incorrecto
**Solución:**
- Verificar labels de los pods
- Verificar selector del Service
- Verificar que los puertos coinciden

---

## CI/CD (GitHub Actions)

### Workflow no se ejecuta
**Causa:** Trigger incorrecto o workflow deshabilitado
**Solución:**
- Verificar el archivo `.github/workflows/ci.yml`
- Verificar la sintaxis YAML
- Verificar que el trigger coincide con la rama/evento

### Build falla en CI pero funciona local
**Causa:** Diferencia de entorno
**Solución:**
- Verificar versión de .NET en `setup-dotnet`
- Verificar que no hay archivos `.user` o locales
- Verificar que las dependencias se restauran correctamente

### Secret no encontrado en pipeline
**Causa:** Secret no está configurado en GitHub
**Solución:**
- Ir a Settings → Secrets → Actions
- Verificar el nombre del secret (case-sensitive)
- Verificar que el secret no expiró

---

## Referencia
- [[00-Index]]
- [[Cheatsheets/Comandos-Docker]]
- [[Cheatsheets/Comandos-Kubernetes]]
