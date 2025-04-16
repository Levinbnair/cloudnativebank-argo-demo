# CloudNativeBank GitOps Final Project

This repository demonstrates a GitOps-based Kubernetes deployment strategy using **Argo CD** and **Argo Rollouts**. It showcases cluster resource management, application lifecycle automation, and safe progressive delivery as part of a modernization effort for a banking platform.

## ✨ Scenario
As part of a digital transformation initiative at **CloudNative Bank**, the legacy monolithic system is being migrated to a Kubernetes-based microservices architecture. This repository contains declarative manifests that are managed and applied using Argo CD and progressively deployed using Argo Rollouts.

## 🌐 Tools Used
- **Argo CD**: GitOps-based application and cluster management
- **Argo Rollouts**: Progressive delivery controller (Canary strategy)
- **Minikube**: Local Kubernetes environment
- **GitHub**: Source of truth for manifests

---

## 🔧 Repository Structure
```
cloudnativebank-argo-demo/
├── guestbook/
│   └── guestbook-app.yaml           # Standard Kubernetes deployment manifest
├── rollouts/
    └── rollout-canary.yaml          # Argo Rollout manifest with canary steps
```

---

## ✅ Task Breakdown

### Task: Cluster Management via Argo CD
- Deploys a namespace and a guestbook microservice via `guestbook/guestbook-app.yaml`
- Managed entirely through GitOps (Argo CD syncs this state from Git)

**To deploy:**
```bash
argocd app create guestbook \  
  --repo https://github.com/Levinbnair/cloudnativebank-argo-demo.git \  
  --path guestbook \  
  --dest-server https://kubernetes.default.svc \  
  --dest-namespace guestbook
```

### Task: Application Sync, Health Check, Auto-Healing
- Demonstrates Git-based application lifecycle changes
- Updates (e.g., replica count or container image) pushed to Git auto-sync to cluster
- Argo CD ensures the cluster state always matches Git
- Deleted pods are automatically recovered (self-healing)

**To sync changes after Git push:**
```bash
argocd app sync guestbook
```

### Task: Progressive Deployment with Argo Rollouts
- Canary deployment of guestbook using `rollouts/rollout-canary.yaml`
- Traffic is shifted in phases: 20% → pause → 60% → pause → promote
- Rollout monitored via CLI and (optionally) dashboard

**To deploy rollout app:**
```bash
argocd app create guestbook-rollout \  
  --repo https://github.com/Levinbnair/cloudnativebank-argo-demo.git \  
  --path rollouts \  
  --dest-server https://kubernetes.default.svc \  
  --dest-namespace guestbook
```

**Promote rollout (if paused):**
```bash
kubectl-argo-rollouts promote guestbook-rollout -n guestbook
```

---

## 🌍 Accessing Argo Interfaces

### Argo CD Dashboard
```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```
Access at: [http://localhost:8080](http://localhost:8080)

### Argo Rollouts Dashboard
```bash
kubectl port-forward svc/argo-rollouts-dashboard -n argo-rollouts 3100:3100
```
Access at: [http://localhost:3100](http://localhost:3100)

---

## 🎓 Author
**Levin Bethur Madhav**  
Course: PRO690V1A - Introduction to DevOps Practices and Principles  
Seneca College of Applied Arts and Technology

