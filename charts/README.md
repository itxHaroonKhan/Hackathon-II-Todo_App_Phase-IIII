# Todo Application Helm Charts

This repository contains Helm charts for deploying the Todo application with separate backend and frontend services, plus an umbrella chart that combines both.

## Chart Structure

```
charts/
├── todo-backend/      # Backend service Helm chart
├── todo-frontend/     # Frontend service Helm chart
├── todo-app/          # Umbrella chart for both services
└── README.md          # This file
```

## Charts Overview

### 1. todo-backend
- FastAPI-based backend service
- Includes database connectivity
- Configurable resources, probes, and HPA

### 2. todo-frontend
- Next.js-based frontend service
- Configurable ingress setup
- Authentication support via environment variables

### 3. todo-app (Umbrella Chart)
- Combines both frontend and backend into a single deployment
- Conditional deployments (enable/disable services as needed)
- Proper template namespacing

## Installation

### Prerequisites

- Kubernetes 1.19+
- Helm 3+
- For Minikube: `minikube start && minikube addons enable ingress`

### Installation Steps

1. **Clone this repository**

2. **Install dependencies** (for the umbrella chart):
   ```bash
   cd charts/todo-app
   helm dependency build
   ```

3. **Install the umbrella chart**:
   ```bash
   # Standard installation
   helm install todo-app charts/todo-app

   # With custom values
   helm install todo-app charts/todo-app -f values-custom.yaml

   # For Minikube
   helm install todo-app charts/todo-app -f values-minikube.yaml
   ```

4. **Access the application**:
   For Minikube, you have multiple options:
   ```bash
   # Option 1: Using port forwarding
   kubectl port-forward svc/todo-app-todo-frontend 3000:80

   # Option 2: Using Minikube service command
   minikube service todo-app-todo-frontend

   # Option 3: If ingress is enabled
   minikube tunnel
   ```

## Chart Configuration

### Backend Configurable Values
- `replicaCount`: Number of backend pods
- `image.repository`: Backend image repository
- `image.tag`: Backend image tag
- `service.type`: Service type (ClusterIP, NodePort, LoadBalancer)
- `service.port`: Service port
- `ingress.enabled`: Enable/disable ingress
- `resources`: CPU/memory requests and limits
- `autoscaling`: Horizontal Pod Autoscaler settings
- `secrets`: Database connection parameters
- `env`: Additional environment variables
- `livenessProbe`/`readinessProbe`: Health check configurations

### Frontend Configurable Values
- `replicaCount`: Number of frontend pods
- `image.repository`: Frontend image repository
- `image.tag`: Frontend image tag
- `service.type`: Service type
- `service.port`: Service port
- `ingress.enabled`: Enable/disable ingress
- `resources`: CPU/memory requests and limits
- `autoscaling`: Horizontal Pod Autoscaler settings
- `secrets`: Application secrets (Auth0, etc.)
- `env`: Additional environment variables
- `livenessProbe`/`readinessProbe`: Health check configurations

### Umbrella Chart Values
The umbrella chart allows enabling/disabling individual services via:

```yaml
backend:
  enabled: true  # Enable/disable backend

frontend:
  enabled: true  # Enable/disable frontend
```

## Secrets Management

The charts handle sensitive information using Kubernetes Secrets:

- **Backend**: Database credentials (host, name, user, password)
- **Frontend**: Authentication secrets (NextAuth, API keys, etc.)

## Probes Configuration

Both services include:
- Liveness probes (for application health checking)
- Readiness probes (to determine when pods are ready for traffic)

## Horizontal Pod Autoscaling (HPA)

Autoscaling is configurable for both services:

- **Backend**: CPU and memory targets (80% utilization default)
- **Frontend**: CPU and memory targets

Example HPA config:
```yaml
autoscaling:
  enabled: true
  minReplicas: 1
  maxReplicas: 10
  targetCPUUtilizationPercentage: 80
```

## Development Setup for Minikube

1. **Start Minikube with ingress**:
   ```bash
   minikube start
   minikube addons enable ingress
   # Enable registry if building from source
   minikube addons enable registry
   ```

2. **Install with Minikube-friendly options**:
   ```bash
   helm install todo-app charts/todo-app -f values-minikube.yaml --set backend.image.pullPolicy=Never
   # The pullPolicy=Never forces using locally built images
   ```

3. **Build and load images locally**:
   ```bash
   # Build your images
   docker build -t todo-backend:latest path/to/backend
   docker build -t todo-frontend:latest path/to/frontend

   # Load into Minikube
   minikube image load todo-backend:latest
   minikube image load todo-frontend:latest
   ```

## Troubleshooting

### Common Issues

1. **Ingress not working**:
   - Ensure ingress controller is running: `minikube addons enable ingress`
   - Check ingress resources: `kubectl get ingress`

2. **Service not accessible**:
   - Check if pods are running: `kubectl get pods`
   - Check service endpoints: `kubectl get svc`
   - Check logs: `kubectl logs deployment/<deployment-name>`

3. **Resource limits causing eviction**:
   - Adjust `resources` values in your custom values file
   - Monitor node resources: `kubectl top nodes`

### Useful Commands
```bash
# Get status
helm status todo-app

# List releases
helm list

# Upgrade
helm upgrade todo-app charts/todo-app -f values.yaml

# Rollback
helm rollback todo-app

# Uninstall
helm uninstall todo-app
```

## Chart Development

### Chart Requirements
- Helm 3+ CLI
- Kubernetes manifest validation
- Template syntax validation

### Testing the Charts
- `helm lint charts/todo-backend` - Validate backend chart
- `helm lint charts/todo-frontend` - Validate frontend chart
- `helm lint charts/todo-app` - Validate umbrella chart
- `helm template todo-test charts/todo-app` - Render templates for inspection