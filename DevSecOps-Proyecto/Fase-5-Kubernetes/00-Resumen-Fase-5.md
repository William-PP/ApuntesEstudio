# Fase 5 — Kubernetes Seguro

## Objetivo
Desplegar la aplicación en Kubernetes con configuraciones de seguridad robustas.

## Conceptos clave

| Concepto | Definición |
|----------|-----------|
| **Pod** | Unidad más pequeña — container o grupo de containers |
| **Deployment** | Define réplicas del Pod, auto-healing |
| **Service** | Acceso estable a los Pods (load balancing) |
| **Ingress** | Router HTTP externo → Services |
| **ConfigMap** | Configuración no sensible |
| **Secret** | Configuración sensible (encriptada) |

## Security Context

### En el Pod (afecta a todos los containers)
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mi-app
spec:
  securityContext:
    runAsNonRoot: true
    runAsUser: 1000
    runAsGroup: 3000
    fsGroup: 2000
    seccompProfile:
      type: RuntimeDefault
  containers:
    - name: api
      image: tu-app:latest
      securityContext:
        allowPrivilegeEscalation: false
        readOnlyRootFilesystem: true
        capabilities:
          drop:
            - ALL
```

### Security Context explicable

| Configuración | Valor | ¿Por qué? |
|---------------|-------|-----------|
| `runAsNonRoot` | `true` | El container NO corre como root |
| `runAsUser` | `1000` | UID específico (no 0 = root) |
| `allowPrivilegeEscalation` | `false` | No puede subir privilegios |
| `readOnlyRootFilesystem` | `true` | Filesystem de solo lectura |
| `capabilities.drop` | `ALL` | Elimina todas las capabilities de Linux |
| `seccompProfile` | `RuntimeDefault` | Syscall filtering |

## Network Policies

### Solo permitir tráfico necesario
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-only-ingress
spec:
  podSelector:
    matchLabels:
      app: mi-api
  policyTypes:
    - Ingress
    - Egress
  ingress:
    - from:
        - namespaceSelector:
            matchLabels:
              name: ingress-nginx
      ports:
        - protocol: TCP
          port: 8080
  egress:
    - to:
        - podSelector:
            matchLabels:
              app: sqlserver
      ports:
        - protocol: TCP
          port: 1433
```

## RBAC (Role-Based Access Control)

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: default
  name: pod-reader
rules:
  - apiGroups: [""]
    resources: ["pods"]
    verbs: ["get", "watch", "list"]

---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods
  namespace: default
subjects:
  - kind: ServiceAccount
    name: mi-app-sa
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

## Sealed Secrets

### ¿Qué es?
Encripta secrets para poder commitearlos de forma segura en Git.

```bash
# Instalar
helm install sealed-secrets sealed-secrets/sealed-secrets

# Encriptar un secret
echo -n 'mi-password' | kubeseal --format yaml > sealed-secret.yaml
```

### Resultado
```yaml
apiVersion: bitnami.com/v1alpha1
kind: SealedSecret
metadata:
  name: mi-secret
spec:
  encryptedData:
    password: AgBy3i4OJSWK+PiTySYZZA9rO...
```

## Deployment completo

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mi-api
  labels:
    app: mi-api
spec:
  replicas: 3
  selector:
    matchLabels:
      app: mi-api
  template:
    metadata:
      labels:
        app: mi-api
    spec:
      securityContext:
        runAsNonRoot: true
        runAsUser: 1000
      containers:
        - name: api
          image: tu-app:latest
          ports:
            - containerPort: 8080
          securityContext:
            allowPrivilegeEscalation: false
            readOnlyRootFilesystem: true
            capabilities:
              drop: ["ALL"]
          resources:
            limits:
              memory: "256Mi"
              cpu: "500m"
            requests:
              memory: "128Mi"
              cpu: "250m"
          livenessProbe:
            httpGet:
              path: /health/live
              port: 8080
            initialDelaySeconds: 10
            periodSeconds: 15
          readinessProbe:
            httpGet:
              path: /health/ready
              port: 8080
            initialDelaySeconds: 5
            periodSeconds: 10
```

## Comandos útiles
```bash
# Ver pods
kubectl get pods

# Ver logs
kubectl logs -f <pod-name>

# Entrar al container
kubectl exec -it <pod-name> -- /bin/bash

# Ver eventos de seguridad
kubectl get events --field-selector reason=Failed

# Verificar security context
kubectl describe pod <pod-name>
```

## Notas personales
- 

## Links Relacionados
- [[00-Resumen-Fase-0]]
- [[Glosario-DevSecOps]]
