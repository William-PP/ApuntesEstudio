# Kubernetes — Security Context

## QUÉ es

Configuración de **seguridad a nivel de Pod y Container** que restringe qué puede hacer el proceso dentro del container.

## POR QUÉ importa

| Sin Security Context | Con Security Context |
|---------------------|---------------------|
| Container corre como root | Container corre como usuario normal |
| Puede instalar software | Filesystem de solo lectura |
| Acceso total al kernel | Sin capabilities peligrosas |
| Sin restricciones de syscall | Seccomp profile activo |

## Configuración completa

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mi-app
spec:
  # SEGURIDAD: A nivel de Pod
  securityContext:
    runAsNonRoot: true
    runAsUser: 1000
    runAsGroup: 3000
    fsGroup: 2000
    seccompProfile:
      type: RuntimeDefault

  containers:
    - name: api
      image: mi-app:latest
      # SEGURIDAD: A nivel de Container
      securityContext:
        allowPrivilegeEscalation: false
        readOnlyRootFilesystem: true
        capabilities:
          drop:
            - ALL
          add:
            - NET_BIND_SERVICE  # Solo si necesita puertos < 1024
```

## Explicación de cada opción

| Opción | Valor | ¿Por qué? |
|--------|-------|-----------|
| `runAsNonRoot` | `true` | **CRÍTICO** — no puede ejecutar como root |
| `runAsUser` | `1000` | UID específico (no 0 = root) |
| `runAsGroup` | `3000` | GID del grupo |
| `fsGroup` | `2000` | Permisos de archivos |
| `allowPrivilegeEscalation` | `false` | No puede subir privilegios |
| `readOnlyRootFilesystem` | `true` | No puede modificar archivos del sistema |
| `capabilities.drop` | `ALL` | Elimina todas las capabilities de Linux |
| `seccompProfile` | `RuntimeDefault` | Filtra syscalls peligrosas |

## DÓNDE aplicar

| Nivel | Qué afecta |
|-------|-----------|
| **Pod spec** | Todos los containers del Pod |
| **Container spec** | Solo ese container específico |
| **Deployment** | Se propaga a todos los Pods creados |

## Validation

```bash
# Verificar que Pods corren como non-root
kubectl get pods -o jsonpath='{.items[*].spec.containers[*].securityContext.runAsNonRoot}'

# Verificar capabilities
kubectl describe pod mi-app | grep -A 5 "Security Context"
```

## Referencia
- [[00-Index]]
- [[Kubernetes/Fundamentos]]
- [[Kubernetes/Network-Policies]]
