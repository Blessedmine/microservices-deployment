Phase 1: Foundation Setup

Environment Preparation

Set up a cloud account (AWS EKS or Azure AKS).

Install local tools: Docker, kubectl, Helm, and IntelJ.

Create a GitHub repository and define the initial project structure.

Phase 2: Kubernetes Deployment

Configure kubectl Access
For EKS:
aws eks update-kubeconfig --name microservice --region us-east-1

Set Up Namespaces & RBAC
Create namespaces:
kubectl create namespace adservice
kubectl create namespace cartservice
kubectl create namespace checkoutservice
kubectl create namespace currencyservice
kubectl create namespace emailservice
kubectl create namespace frontend
kubectl create namespace paymentservice
kubectl create namespace productcatalogue
kubectl create namespace recommendationservice
kubectl create namespace shippingservice
kubectl create namespace redis-cart

2.2 Service Deployment
Steps:
1. Create Kubernetes Manifests
2. Deploy Service

### **2.2 Service Deployment**
#### **Steps:**
1. **Create Kubernetes Manifests for the microservices**

Configure Ingress

Install NGINX Ingress Controller:
helm install ingress-nginx ingress-nginx/ingress-nginx
Define ingress.yaml for routing.
Add the Helm repository (if not already added)
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx helm repo update

kubectl apply -f ingress.yaml

Install NGINX Ingress Controller in a dedicated namespace
helm upgrade --install ingress-nginx ingress-nginx/ingress-nginx --namespace ingress-nginx --create-namespace --set controller.service.type=LoadBalancer

Check if Helm release exists

helm list -A

kubectl get deployments -n ingress-nginx 
kubectl get pods -n ingress-nginx 
kubectl get ingress -A 
kubectl get svc -n ingress-nginx

3.1 Scaling & Secrets
Steps:
Enable Horizontal Pod Autoscaler (HPA) for automatic scaling.

Manage sensitive data with Secrets.

Use ConfigMaps for environment-specific configurations.

Add readiness and liveness probes for better reliability.


