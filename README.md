# Cloud Infrastructure Operations

This repository contains the GitOps configuration for managing cloud infrastructure using ArgoCD. It follows a multi-tenant architecture with external and internal separation.But for better security we should use seperate cluster for all resources

##  Repository Structure

```
cloud-infra-ops/
├── apps/
│   ├── baseconfig/           # Base configurations for common services
│   │   ├── postgres/         # PostgreSQL base configurations
│   │   ├── rabbitmq/         # RabbitMQ base configurations
│   │   └── redis/            # Redis base configurations
│   ├── external-charts/      # External Helm charts (currently empty)
│   └── internal-charts/      # Internal application charts
│       ├── control-plane/    # Control plane application
│       ├── control-plane-tcp-gateway/  # TCP gateway for control plane
│       ├── db/               # Database services
│       ├── frontend/         # Frontend application
│       ├── https-server/     # HTTPS server configuration
│       ├── infra-provisioner/ # Infrastructure provisioning
│       ├── istio-ingress/    # Istio ingress configuration
│       ├── prometheus/       # Monitoring stack
│       ├── queue/            # Message queue services
│       ├── redis/            # Redis services
│       └── sealed-secret/    # Sealed secrets configuration
├── argocd/                   # ArgoCD application definitions
│   ├── internal/             # Internal applications
│   │   ├── amqp-protocol-gateway.yaml
│   │   ├── control-plane.yaml
│   │   ├── db.yaml
│   │   ├── frontend.yaml
│   │   ├── https-server.yaml
│   │   ├── infra-provisioner.yaml
│   │   ├── istio-ingress.yaml
│   │   ├── pg-protocol-gateway.yaml
│   │   ├── queue.yaml
│   │   ├── redis-gateway.yaml
│   │   ├── redis.yaml
│   │   └── sealed-secret.yaml
│   └── external/             # External applications
└── cloud-root.yaml           # Root ArgoCD application (entry point)
```

##  Architecture

The infrastructure follows a multi-tenant architecture with two main environments:

1. **Internal**: Core infrastructure services and internal applications
   - Control Plane & TCP Gateway
   - Database Services (PostgreSQL, Redis)
   - Message Queue (RabbitMQ)
   - Infrastructure Provisioning
   - Sealed Secrets Management
   - TCP Gateways (AMQP, PostgreSQL, Redis)
   - Istio Ingress for traffic management


2. **External**: Customer-facing services
   - All customer facing applications


1. Apply the root application to bootstrap the infrastructure:
   ```bash
   kubectl apply -f cloud-root.yaml or any it auto apply
   ```

2. Access ArgoCD UI:
   ```bash
   kubectl port-forward svc/argocd-server -n argocd 8080:80
   ```
   Open http://localhost:8080 in your browser

## Deployment Flow

1. Changes pushed to the main branch trigger ArgoCD to synchronize the desired state
2. ArgoCD compares the desired state with the current state
3. Required resources are created/updated/deleted to match the desired state


### Gateways
- **AMQP Protocol Gateway**: Handles AMQP protocol translation
- **PostgreSQL Gateway**: Manages PostgreSQL connections
- **Redis Gateway**: Handles Redis protocol


##  Security
- **Sealed Secrets**: Encrypted secrets in Git
- **Network Policies**: Pod-to-pod communication rules
- **RBAC**: Fine-grained access control
- **TLS**: Encrypted communication

## Networking
- **Istio Ingress**: Traffic management and load balancing
- **Service Mesh**: Internal service-to-service communication
- **Gateways**: Protocol-specific entry points
