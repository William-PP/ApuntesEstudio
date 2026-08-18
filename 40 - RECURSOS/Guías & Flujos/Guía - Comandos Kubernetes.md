---
type: guía
state: activa
created: 2026-08-15
updated: 2026-08-15
tags: [cheatsheets, debug, comandos, devsecops, estudio, logging, secrets, deploy]
aliases: [Comandos-Kubernetes]
---

# Guía - Comandos Kubernetes

## Comandos esenciales

```bash
# ═══════════════════════════════════════════════════
# GENERAL
# ═══════════════════════════════════════════════════
kubectl cluster-info                # Info del cluster
kubectl get nodes                   # Ver nodes
kubectl top nodes                   # Uso de recursos
kubectl version                     # Versión

# ═══════════════════════════════════════════════════
# PODS
# ═══════════════════════════════════════════════════
kubectl get pods                    # Ver pods
kubectl get pods -o wide            # Con IPs
kubectl get pods -w                 # Watch (tiempo real)
kubectl describe pod pod-name       # Info detallada
kubectl logs pod-name               # Logs
kubectl logs -f pod-name            # Logs seguimiento
kubectl exec -it pod-name -- /bin/bash  # Entrar
kubectl delete pod pod-name         # Eliminar

# ═══════════════════════════════════════════════════
# DEPLOYMENTS
# ═══════════════════════════════════════════════════
kubectl get deployments             # Ver deployments
kubectl apply -f deployment.yaml    # Aplicar
kubectl rollout status deployment/name  # Estado
kubectl rollout undo deployment/name    # Rollback
kubectl scale deployment/name --replicas=3  # Escalar

# ═══════════════════════════════════════════════════
# SERVICES
# ═══════════════════════════════════════════════════
kubectl get services                # Ver services
kubectl apply -f service.yaml       # Aplicar

# ═══════════════════════════════════════════════════
# SECRETS
# ═══════════════════════════════════════════════════
kubectl get secrets                 # Ver secrets
kubectl create secret generic name --from-literal=key=value
kubectl describe secret name        # Info (sin ver valores)

# ═══════════════════════════════════════════════════
# DEBUGGING
# ═══════════════════════════════════════════════════
kubectl get events                  # Eventos
kubectl get events --sort-by=.lastTimestamp  # Ordenados
kubectl get events --field-selector reason=Failed  # Errores
kubectl port-forward pod-name 8080:8080  # Port forward
```

## Referencia
- [[40 - RECURSOS/MOCs/MOC - Cheatsheets]]

#cheatsheets #debug #comandos #devsecops #estudio #logging #secrets #deploy
