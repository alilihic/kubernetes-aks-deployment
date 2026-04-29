# AKS cluster deployment with Helm, RBAC and Azure Monitor integration

Production-grade Kubernetes manifests for deploying a containerised web application on Azure Kubernetes Service (AKS). Includes security hardening, autoscaling, ingress with TLS, and network policies.

## What's included

| File | Description |
|---|---|
| `namespace.yaml` | Namespace, ResourceQuota, NetworkPolicy |
| `deployment.yaml` | Deployment, Service, Ingress, HPA, PDB, ServiceAccount |

## Features

- **3-replica deployment** with rolling update strategy (zero downtime)
- **HPA** — scales 3→10 pods based on CPU (70%) and memory (80%) utilisation
- **PodDisruptionBudget** — minimum 2 pods available during node maintenance
- **Liveness + Readiness probes** — automatic traffic routing and restart logic
- **Non-root security context** — read-only filesystem, dropped capabilities
- **Workload Identity** — Azure Managed Identity via ServiceAccount annotation
- **Network Policy** — ingress from nginx only, egress to DNS + HTTPS
- **Resource quotas** — namespace-level CPU/memory limits
- **TLS Ingress** — cert-manager + Let's Encrypt, HTTPS enforced
- **Topology spread** — pods distributed across nodes

## Prerequisites

- AKS cluster running
- `kubectl` configured
- `cert-manager` installed in cluster
- `ingress-nginx` installed
- Azure Container Registry with your image pushed

## Deploy

```bash
# Apply namespace and policies first
kubectl apply -f namespace.yaml

# Create secrets
kubectl create secret generic webapp-secrets \
  --from-literal=db-connection-string="your-connection-string" \
  -n production

# Deploy application
kubectl apply -f deployment.yaml

# Verify
kubectl get pods -n production
kubectl get hpa -n production
kubectl get ingress -n production
```

## Verify scaling

```bash
# Watch HPA
kubectl get hpa webapp-hpa -n production --watch

# Check pod distribution across nodes
kubectl get pods -n production -o wide
```

## Author

Ali Lihic — [linkedin.com/in/ali-lihic](https://linkedin.com/in/ali-lihic)
