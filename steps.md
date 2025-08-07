## Let’s Start with Hands-on/Practical of Argo Rollouts with Canary Deployments.

###Prerequisites
Pre-Install Ubuntu 24.04 LTS Sudo User with admin privileges 32 GB RAM or more  8CPU / vCPU or more 50 GB free hard disk space or more Docker / Virtual Machine Manager – KVM & VirtualBox

##Install Docker
sudo apt update

# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
$(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin 

sudo usermod -aG docker $USER
newgrp docker

##Install Minikube

curl -LO https://github.com/kubernetes/minikube/releases/latest/download/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube && rm minikube-linux-amd64

minikube start --cpu=8 --memory=14000

###Install Kubectl

curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

##Install Argo Rollouts controller on Minikube

Create the namespace for installation of the Argo Rollouts controller

kubectl create namespace argo-rollouts
kubectl apply -n argo-rollouts -f https://github.com/argoproj/argo-rollouts/releases/latest/download/install.yaml
Now, you will see the controller and other components have been deployed. Wait for the pods to be in the Running state.

kubectl get all -n argo-rollouts
Install Argo Rollouts Kubectl plugin with curl for easy interaction with Rollout controller and resources with below command.

curl -LO https://github.com/argoproj/argo-rollouts/releases/latest/download/kubectl-argo-rollouts-linux-amd64
chmod +x ./kubectl-argo-rollouts-linux-amd64
sudo mv ./kubectl-argo-rollouts-linux-amd64 /usr/local/bin/kubectl-argo-rollouts
kubectl argo rollouts version

Argo Rollouts comes with its own GUI as well that you can access with the below command.

kubectl argo rollouts dashboard 

Now, you can access Argo Rollout console, by accessing http://publicIP:3100 on your browser. 

##Download and Install Istio

Go to the Istio release page to download the installation file for your OS, or download and extract the latest release automatically (Linux or macOS):

$ curl -L https://istio.io/downloadIstio | sh -

Move to the Istio package directory. For example, if the package is istio-1.26.3:

$ cd istio-1.26.3
$ ls

The installation directory contains:

Sample applications in samples/
The istioctl client binary in the bin/ directory.

Add the istioctl client to your path (Linux or macOS):

$ export PATH=$PWD/bin:$PATH

##Install Istio

istioctl install -y

##Installing istio into your application
You will specify the label
Add a namespace label to instruct Istio to automatically inject Envoy sidecar proxies when you deploy your application later
$ kubectl label namespace default istio-injection=enabled
namespace/default labeled
with the command, it will install the istio to the default namespace

## To check the folder in the istio do:
ls 
cd samples/addons
ls
 To get the details like the grafana.yaml, jseger.yaml,prometheus.yaml,kiali.yaml for your observability etc

## Istio integrates with several different telemetry applications. These can help you gain an understanding of the structure of your service mesh, display the topology of the mesh, and analyze the health of your mesh.

Use the following instructions to deploy the Kiali dashboard, along with Prometheus, Grafana, and Jaeger.

Install Kiali and the other addons and wait for them to be deployed.

$ kubectl apply -f samples/addons
$ kubectl rollout status deployment/kiali -n istio-system
Waiting for deployment "kiali" rollout to finish: 0 of 1 updated replicas are available...
deployment "kiali" successfully rolled out

#get the service in the istio namespace
kubectl get svc -n istio-system

port forward to access the kiali
kubectl port-forward svc/kiali -n istio-system --address 0.0.0.0 20001:20001

##Set up ArgoCD Install ArgoCD in Minikube Namespace argocd

kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

kubectl get all n argocd
kubectl get pods -n argocd
kubectl get svc -n argocd

check for the port the argo server is on 

Expose ArgoCD Server First, add port 8080 to Inbound Rules for the EC2 Instance

kubectl port-forward --address 0.0.0.0 svc/argocd-server 8080:443 -n argocd 

##Get ArgoCD Initial Admin Password

To access ArgoCD on the browser:

PublicIP:8080
ARGOCD_USERNAME: admin
ARGOCD_PASSWORD:

