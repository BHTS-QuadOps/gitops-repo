# GitOps Repository (Ansible + ArgoCD)

This repository contains the **infrastructure and deployment configuration** for the TaskApp platform.  
It works together with the main application repository:

➡️ Application repo (builds Docker images):  
[https://github.com/<BHTS-QuadOps>/Tasklist-App](https://github.com/BHTS-QuadOps/Tasklist-App)

➡️ GitOps repo (this repository):  
Manages Kubernetes deployments using **ArgoCD** and **Ansible**.

---

## 🚀 Architecture Overview

### 🏗 Application Repository (Tasklist-App)
- Contains Spring Boot source code and Dockerfile  
- GitHub Actions CI builds the JAR  
- CI builds + pushes Docker images to GHCR  
- No Kubernetes manifests are stored there  

### 🔁 GitOps Repository (this repo)
- Contains **deployment configuration only**  
- ArgoCD watches this repo and applies changes automatically  
- Ansible configures and bootstraps the Kubernetes cluster  
- Kubernetes manifests define the desired state of:
  - `taskapp` deployment  
  - PostgreSQL database  
  - Ingress routes  
  - Secrets  
  - Namespaces  
  - Add-ons  

---

## 📁 Repository Structure
```
gitops-repo/
│
├── ansible/                     # Infrastructure automation (provisioning)
│   ├── inventories/
│   ├── roles/
│   └── playbooks/
│
├── argocd/                      # ArgoCD configuration ("App of Apps")
│   ├── apps/                    # ArgoCD Application CRDs
│   │   ├── taskapp.yaml
│   │   ├── postgres.yaml
│   │   └── ingress.yaml
│   ├── projects/                # ArgoCD projects (RBAC & scoping)
│   │   └── default-project.yaml
│   └── bootstrap/               # ArgoCD installation
│       └── argocd-install.yaml
│
├── apps/                        # Kubernetes application manifests
│   ├── taskapp/
│   │   ├── deployment.yaml
│   │   ├── service.yaml
│   │   ├── ingress.yaml
│   │   ├── kustomization.yaml
│   │   └── secrets.yaml         # (Encrypted with SOPS or Sealed Secrets)
│   │
│   └── postgres/
│       ├── statefulset.yaml
│       ├── service.yaml
│       ├── pvc.yaml
│       └── kustomization.yaml
│
├── clusters/                    # Environment-specific overlays
│   ├── dev/
│   │   ├── kustomization.yaml
│   │   ├── taskapp.yaml
│   │   └── postgres.yaml
│   │
│   ├── staging/
│   │   ├── kustomization.yaml
│   │   ├── taskapp.yaml
│   │   └── postgres.yaml
│   │
│   └── prod/
│       ├── kustomization.yaml
│       ├── taskapp.yaml
│       └── postgres.yaml
│
└── README.md
```
---

## 🔧 Workflow Summary

### 1️⃣ **Developer pushes code → Tasklist-App repo**
GitHub Actions:
- Builds Spring Boot JAR  
- Builds Docker image  
- Pushes image to GHCR (GitHub Container Registry)  

### 2️⃣ **Manifest update → GitOps repo**
- Update image tag in `apps/taskapp/deployment.yaml`  
- Commit & push  

### 3️⃣ **ArgoCD auto-sync**
ArgoCD:
- Detects commit in this GitOps repo  
- Applies changes to the Kubernetes cluster  
- Ensures cluster state matches Git

---

## 🧩 Why Separate Repos?

### ✔ Better security  
Application repo does **not** contain secrets or cluster config.

### ✔ Clear separation of concerns  
- App repo = code  
- GitOps repo = deployment & infrastructure  

### ✔ Cleaner CI/CD  
Each repo has a single responsibility.

---

## 🛠 Tech Stack

- **ArgoCD** → GitOps continuous delivery  
- **Ansible** → Cluster bootstrap & configuration  
- **MicroK8s** → Kubernetes runtime  
- **GHCR** → Docker image hosting  
- **Spring Boot** → Application  
- **PostgreSQL** → Database  

---

## 🤝 Contributing

Pull requests welcome!  
Follow GitOps principles: all changes to infrastructure and manifests must go through this repository.

---
