🚀 Project Workflow: GitOps-Driven Kubernetes Deployment of 11 Microservices
Phase 1: Foundation Setup
1.1 Environment Preparation

Cloud Setup:

Provision an Amazon EKS (Elastic Kubernetes Service) cluster.

Configure the cluster with 3 worker nodes to host the 11 microservices efficiently.

Local Tools Installation:
Install required tools on your workstation:

Docker

kubectl

IntelliJ IDEA (IDE for microservices development)

Version Control:
Create a GitHub repository and define the initial project structure (monorepo for all microservices + Kubernetes manifests).

Phase 2: Kubernetes Deployment
2.1 Cluster Access & Namespaces

Configure kubectl for EKS:

aws eks update-kubeconfig --name microservice --region us-east-1


Create Namespaces:

kubectl create namespace dev
kubectl create namespace staging
kubectl create namespace prod
kubectl create namespace argocd

2.2 Service Deployment
Step 1: Create Kubernetes Manifests

Write Deployment and Service manifests for all 11 microservices.

Organize them into a base folder, and create overlays (dev, staging, prod) using Kustomize.

Step 2: Configure Ingress

Install NGINX Ingress Controller:

helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
helm upgrade --install ingress-nginx ingress-nginx/ingress-nginx \
--namespace ingress-nginx \
--create-namespace \
--set controller.service.type=LoadBalancer


Verify installation:

kubectl get deployments -n ingress-nginx
kubectl get pods -n ingress-nginx
kubectl get svc -n ingress-nginx


Apply ingress routing rules:

kubectl apply -f ingress.yaml

2.3 Scaling & Configuration Management

Enable Horizontal Pod Autoscaler (HPA):
Scale services automatically based on CPU/memory utilization.

Manage Configurations:

Use ConfigMaps for environment-specific configs.

Use Secrets for sensitive data (e.g., database passwords, API keys).

Reliability Enhancements:

Add liveness and readiness probes to all services.

2.4 Multi-Environment Deployment with Kustomize

Deploy manifests for each environment:

# Development
kubectl apply -k overlays/dev

# Staging
kubectl apply -k overlays/staging

# Production
kubectl apply -k overlays/prod


Note:

Only the frontend service has 3 environments (/dev, /staging, /prod) since it’s the entry point for users.

All back-end services are shared across environments to keep the setup simple.

All deployments are distributed across the 3 worker nodes for availability and load balancing.

Phase 3: GitOps with ArgoCD
3.1 Install ArgoCD
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

3.2 Configure ArgoCD Applications

Create an argocd/applications.yaml manifest defining dev, staging, and prod applications.

Apply it:

kubectl apply -f argocd/applications.yaml

3.3 Access the ArgoCD Web UI

Check ArgoCD service:

kubectl get svc -n argocd


Port-forward (local access):

kubectl port-forward svc/argocd-server -n argocd 8080:443


Open: 👉 https://localhost:8080

Login Credentials:

Username: admin

Password:

kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo


After login, you’ll see your Applications (dev, staging, prod) in the ArgoCD dashboard.

✅ Final Outcome

This project provides an end-to-end DevOps workflow:

Develop and containerize 11 microservices.

Deploy on Kubernetes using Kustomize and Ingress.

Run workloads across a 3-node EKS cluster for scalability and resilience.

Manage scaling, secrets, and configs.

Enable GitOps-driven delivery with ArgoCD.

Support multi-environment deployments (dev, staging, prod).