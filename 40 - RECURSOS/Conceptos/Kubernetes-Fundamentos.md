---
type: concepto
state: activa
created: 2026-08-15
updated: 2026-08-15
tags: [config, devsecops, containers, cloud, api, estudio, microservices, kubernetes, fundamentos, k8s, deploy]
---

# Kubernetes — Fundamentos

## QUÉ es

Kubernetes (K8s) es una plataforma de **orquestación de contenedores** que automatiza el despliegue, escalado y gestión de containers.

## Conceptos clave

| Concepto | Definición | Analogía |
|----------|-----------|---------|
| **Pod** | Unidad más pequeña (1+ containers) | La celda más pequeña |
| **Deployment** | Gestiona réplicas de Pods | El manager de la fábrica |
| **Service** | Acceso estable a Pods | El número de teléfono de la fábrica |
| **Ingress** | Router HTTP externo | La puerta principal |
| **ConfigMap** | Config no sensible | El manual de instrucciones |
| **Secret** | Config sensible | La caja fuerte |
| **Namespace** | Aislamiento de recursos | Departamentos de la empresa |
| **Node** | Máquina física/virtual | El terreno donde está la fábrica |
| **Cluster** | Grupo de nodes | Complejo industrial |

## Arquitectura

```
                    ┌──────────────────────────────────┐
                    │         Kubernetes Cluster        │
                    │                                   │
                    │  ┌─────────┐  ┌─────────┐       │
                    │  │  Node 1 │  │  Node 2 │       │
                    │  │         │  │         │       │
                    │  │ ┌─────┐ │  │ ┌─────┐ │       │
                    │  │ │Pod 1│ │  │ │Pod 3│ │       │
                    │  │ │ API │ │  │ │ API │ │       │
                    │  │ └─────┘ │  │ └─────┘ │       │
                    │  │ ┌─────┐ │  │ ┌─────┐ │       │
                    │  │ │Pod 2│ │  │ │Pod 4│ │       │
                    │  │ │ DB  │ │  │ │ API │ │       │
                    │  │ └─────┘ │  │ └─────┘ │       │
                    │  └─────────┘  └─────────┘       │
                    │                                   │
                    └──────────────────────────────────┘
```

## CÓMO funciona

```
1. Defines Deployment (quiero 3 réplicas de mi API)
         │
         ▼
2. K8s crea 3 Pods en diferentes Nodes
         │
         ▼
3. Service distribuye tráfico entre los Pods
         │
         ▼
4. Si un Pod falla → K8s crea uno nuevo automáticamente
```

## CUÁNDO usar

| Caso | ¿K8s? |
|------|-------|
| Microservicios | ✅ Sí |
| Apps que necesitan escalado | ✅ Sí |
| Múltiples ambientes | ✅ Sí |
| Monolito pequeño | ❌ Docker Compose basta |
| Desarrollo local | ⚠️ Minikube/Kind |

## DÓNDE desplegar

| Opción | Proveedor | Costo |
|--------|----------|-------|
| **AKS** | Azure | Free tier disponible |
| **EKS** | AWS | Free tier |
| **GKE** | GCP | Free tier |
| **Minikube** | Local | Gratis |
| **Kind** | Local (Docker) | Gratis |

## Referencia
- [[40 - RECURSOS/MOCs/MOC - Kubernetes]]

#config #devsecops #containers #cloud #api #estudio #microservices #kubernetes #fundamentos #k8s #deploy
