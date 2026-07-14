# Kubernetes — Network Policies

## QUÉ es

Reglas que **controlan el tráfico de red** entre Pods, namespaces y endpoints externos.

## POR QUÉ importa

| Sin Network Policy | Con Network Policy |
|-------------------|-------------------|
| Todos los Pods se comunican entre sí | Solo comunicación autorizada |
| Un Pod comprometido puede acceder a todos | Aislamiento efectivo |
| Sin segmentación de red | Micro-segmentación |

## CÓMO funciona

```yaml
# Por defecto: SIN restricciones
Pod A ←→ Pod B ←→ Pod C  (todo permitido)

# Con Network Policy:
Pod A ←→ Pod B    Pod C (aislado)
  │
  └── Solo tráfico permitido
```

## Ejemplo: Solo permitir ingress desde el namespace de ingress

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-only-ingress
  namespace: default
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

## Reglas comunes

| Regla | Propósito |
|-------|-----------|
| Solo ingress desde Ingress Controller | Tráfico externo solo por la puerta |
| Solo egress a BD | La API solo habla con la BD |
| Deny all por defecto | Zero trust networking |
| Allow within namespace | Pods del mismo namespace se comunican |

## CUÁNDO usar

- **SIEMPRE** en producción
- Microservicios con sensibilidad
- Cuando necesitas zero trust

## Referencia
- [[DevSecOps-Estudio/00-Index]]
- [[Kubernetes/Security-Context]]
- [[Kubernetes/Fundamentos]]
