## 📘 GitOps Project with ArgoCD and Helm

This repository contains Infrastructure as Code (IaC) and application configurations for automated Kubernetes deployments using the GitOps approach with ArgoCD and Helm charts.
## 📌 Overview

    Type: GitOps

    Orchestrator: Kubernetes

    Tool: ArgoCD

    Manifest Management: Helm

    Goal: Automatic cluster deployment and synchronization from Git repository

## 🧩 Project Structure

```
my-app/                # Sample Helm chart
  ├── Chart.yaml         # Chart metadata
  ├── values.yaml        # Configuration values
  └── templates/         # Kubernetes manifest templates
      ├── deployment.yaml
      └── service.yaml
```
## 🚀 How to Use

###1. Install Minikube (local cluster)
```
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
minikube start --driver=docker
```
### 2. Install ArgoCD
```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/v2.8.4/manifests/install.yaml
kubectl port-forward svc/argocd-server -n argocd 8080:443
```
### 3. Login to ArgoCD
```
argocd login localhost:8080 --insecure
```
Use username: admin
Get password:
```
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```
### 4. Create Application via CLI
```
argocd app create my-app \
  --repo https://github.com/<your-username>/gitops-demo-repo.git \
  --path charts/my-app \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace default \
  --helm-chart my-app \
  --auto-prune \
  --sync-policy automated
```
### 5. Via ArgoCD UI
```
Visit: https://localhost:8080
Click + New App
Configure:
    Repository URL: https://github.com/<your-username>/gitops-demo-repo.git
    Path: charts/my-app
    Cluster URL: https://kubernetes.default.svc
    Namespace: default
    Sync Policy: ✅ Automated
Click Create → Sync
```
### 🔁 Automatic Synchronization

Any repository changes will be automatically applied:
```
git add .
git commit -m "Update config"
git push origin main
```
ArgoCD detects changes and syncs automatically.

###🛡️ Self-healing

Manual cluster changes are reverted:
```
kubectl scale deployment my-app --replicas=5
```
ArgoCD restores desired Git state.

### 🔐 Security

Change default admin password:
```
argocd account update-password
```
For private repos, configure SSH key:
```
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```
Add public key (~/.ssh/id_rsa.pub) in ArgoCD → Settings → Repositories

###📊 Monitoring & Troubleshooting

Useful commands:
```
argocd app list
argocd app get my-app
argocd app sync my-app
argocd app history my-app
kubectl logs -n argocd
```
Common issues:
```
repository not accessible  → Check repo access rights
comparison error          → Run sync or verify manifests
invalid chart path        → Ensure Chart.yaml exists
```
### ✅ What You Get

    Automated deployments: Git changes → cluster updates

    Self-healing: Cluster matches Git state

    Change history: Track and rollback versions

    Scalability: Add apps and environments (dev/staging/prod)
---
### 🙌 Author

🪪 dmplastun

📧 dmitrij.plastun@gmail.com

🔗 https://dmplastun.github.io/gitops-demo-repo/
