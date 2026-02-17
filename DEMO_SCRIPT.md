# Demo Script: Deploy, Break, Heal with AI

## Introduction
Welcome to our demonstration of AI-assisted Kubernetes operations for the Todo application. Today we'll showcase how AI tools like kubectl-ai and kagent can handle deployment, troubleshooting, and optimization automatically.

## Setup
1. Ensure Minikube is running with ingress addon enabled
2. Have the Helm charts ready in the charts/ directory

## Phase 1: Automated Deployment with AI
1. Use kubectl-ai to deploy the application:
   ```
   kubectl ai "deploy the todo-app using the Helm chart from charts/todo-app"
   ```

2. Verify the deployment with kubectl-ai:
   ```
   kubectl ai "show me the status of all pods in the todo application"
   ```

## Phase 2: AI-Powered Optimization
1. Use kagent to analyze the current deployment:
   ```
   kagent analyze todo-app
   ```

2. Apply kagent's optimization recommendations:
   ```
   kagent optimize todo-app
   ```

## Phase 3: Simulate Failure and AI Self-Healing
1. Simulate a pod failure:
   ```
   kubectl delete pod <any-pod-name-from-todo-app>
   ```

2. Use kagent to troubleshoot and heal:
   ```
   kagent troubleshoot deployment/todo-app
   ```

3. Verify healing occurred:
   ```
   kubectl get pods
   ```

## Phase 4: AI-Driven Scaling
1. Use kubectl-ai to scale the application based on demand:
   ```
   kubectl ai "scale the frontend deployment to 3 replicas"
   ```

2. Verify scaling with kubectl-ai:
   ```
   kubectl ai "show me how many replicas the frontend deployment has"
   ```

## Phase 5: Health Monitoring with AI
1. Use kagent for ongoing health analysis:
   ```
   kagent monitor todo-app
   ```

2. Check for any recommendations:
   ```
   kagent recommendations todo-app
   ```

## Conclusion
This demonstration showed how AI tools can automate complex Kubernetes operations including deployment, optimization, troubleshooting, scaling, and monitoring. The system exhibits self-healing properties and can adapt to changing conditions automatically.

## Key Takeaways
- Zero manual YAML creation required
- Automated troubleshooting and healing
- Intelligent resource optimization
- Dynamic scaling based on demand
- Continuous health monitoring and recommendations