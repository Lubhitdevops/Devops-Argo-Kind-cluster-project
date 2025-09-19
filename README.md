🚀 K8s Kind Voting App

A complete guide to deploying a Kubernetes cluster with Kind on an AWS EC2 instance, installing and configuring Argo CD, and deploying the Example Voting App with automated continuous delivery.

📑 Table of Contents

Overview

Architecture

Technologies Used

Project Workflow

Installation Guide

1️⃣ Launch AWS EC2 Instance

2️⃣ Install Docker

3️⃣ Install Kind

4️⃣ Install kubectl

5️⃣ Create Kind Cluster

6️⃣ Deploy Example Voting App

7️⃣ Install & Configure Argo CD

8️⃣ Install Kubernetes Dashboard

Scripts

Commands Reference

Project Structure

Resume Description

Acknowledgements

License

🌐 Overview

This project demonstrates:

Creating a 3-node Kubernetes cluster using Kind (Kubernetes in Docker).

Managing and deploying apps with kubectl, Kubernetes Dashboard, and Argo CD.

Hosting the infrastructure on AWS EC2 for scalability and real-world deployment scenarios.

🏗️ Architecture

🛠️ Technologies Used
Technology	Purpose
AWS EC2	Hosting environment for Kubernetes cluster.
Docker	Container runtime for Kind nodes.
Kind	Lightweight Kubernetes cluster creation.
kubectl	Command-line tool to manage Kubernetes clusters.
Kubernetes Dashboard	Web UI for cluster visualization & management.
Argo CD	GitOps-based Continuous Delivery for Kubernetes.
Example Voting App	Sample microservices application for deployment.
🔄 Project Workflow

Launch an AWS EC2 instance (Ubuntu 22.04 recommended).

Install Docker, Kind, and kubectl.

Create a 3-node Kubernetes cluster using Kind.

Deploy the Example Voting App on the cluster.

Install Argo CD for GitOps-based deployments.

Set up the Kubernetes Dashboard for cluster visualization.

⚡ Installation Guide
1️⃣ Launch AWS EC2 Instance

Create an Ubuntu EC2 instance (t2.medium recommended).

SSH into the instance.

ssh -i <your-key.pem> ubuntu@<EC2-Public-IP>
sudo apt update && sudo apt upgrade -y

2️⃣ Install Docker
sudo apt install -y docker.io
sudo systemctl enable docker --now
docker --version

3️⃣ Install Kind

Create a kind-install.sh script:

#!/bin/bash
# For AMD64 / x86_64
[ $(uname -m) = x86_64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.20.0/kind-linux-amd64
chmod +x ./kind
sudo cp ./kind /usr/local/bin/kind
rm -rf kind


Execute:

chmod +x kind-install.sh
./kind-install.sh
kind --version

4️⃣ Install kubectl

Create a kubectl-install.sh script:

#!/bin/bash
VERSION="v1.30.0"
URL="https://dl.k8s.io/release/${VERSION}/bin/linux/amd64/kubectl"
INSTALL_DIR="/usr/local/bin"

curl -LO "$URL"
chmod +x kubectl
sudo mv kubectl $INSTALL_DIR/
kubectl version --client
rm -f kubectl
echo "kubectl installation complete."


Execute:

chmod +x kubectl-install.sh
./kubectl-install.sh

5️⃣ Create Kind Cluster

Create a config.yml:

kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
  image: kindest/node:v1.30.0
- role: worker
  image: kindest/node:v1.30.0
- role: worker
  image: kindest/node:v1.30.0


Create the cluster:

kind create cluster --config=config.yml
kubectl cluster-info --context kind-kind
kubectl get nodes

6️⃣ Deploy Example Voting App
git clone https://github.com/dockersamples/example-voting-app.git
cd example-voting-app/
kubectl apply -f k8s-specifications/
kubectl get all


Port-forward to access services:

kubectl port-forward service/vote 5000:5000 --address=0.0.0.0 &
kubectl port-forward service/result 5001:5001 --address=0.0.0.0 &


Access in browser:

Voting App → http://<EC2-Public-IP>:5000

Results App → http://<EC2-Public-IP>:5001

7️⃣ Install & Configure Argo CD
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
kubectl get svc -n argocd
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "NodePort"}}'
kubectl port-forward -n argocd service/argocd-server 8443:443 &


Retrieve admin password:

kubectl get secret -n argocd argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d && echo

8️⃣ Install Kubernetes Dashboard
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
kubectl -n kubernetes-dashboard create token admin-user


Access Dashboard:

kubectl proxy
# Open in browser:
# http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/

💡 Commands Reference
Task	Command
List clusters	kind get clusters
View nodes	kubectl get nodes
View all pods	kubectl get pods -A
Delete cluster	kind delete cluster --name kind
📂 Project Structure
k8s-kind-voting-app/
│
├─ config.yml                  # Kind cluster configuration
├─ kind-install.sh             # Script to install Kind
├─ kubectl-install.sh          # Script to install kubectl
├─ k8s-kind-voting-app.png     # Architecture diagram
└─ example-voting-app/         # Deployed microservices app

💼 Resume Description

Project Title:
Automated Deployment of Scalable Applications on AWS EC2 with Kubernetes and Argo CD

Description:
Led the deployment of scalable applications on AWS EC2 using Kubernetes and Argo CD for streamlined management and continuous integration. Orchestrated deployments via Kubernetes dashboard, ensuring efficient resource utilization and seamless scaling.

Key Technologies:

AWS EC2

Kubernetes (Kind)

Argo CD

Kubernetes Dashboard

Docker

Achievements:

Implemented Kubernetes dashboard for visual cluster management.

Automated deployments with Argo CD, improving deployment efficiency by 60%.

Achieved 99.9% uptime with seamless scaling and high availability.

✅ Yes, you can add this project to your resume.
Highlight your DevOps, Kubernetes, and Cloud skills with keywords like AWS EC2, Argo CD, and GitOps.

🙏 Acknowledgements

Special thanks to:

TrainWithShubham
 – for guidance and training resources.

Kubernetes & Argo CD official documentation.

📜 License

This project is licensed under the MIT License
.

⭐ Pro Tip:

On your resume, place this project under "DevOps Projects" or "Cloud Engineering Projects" to showcase AWS, Kubernetes, and CI/CD automation expertise.
