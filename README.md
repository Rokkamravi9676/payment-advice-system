# 💳 Payment Advice System — DevOps Implementation

<div align="center">

![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?style=for-the-badge&logo=kubernetes&logoColor=white)
![Jenkins](https://img.shields.io/badge/Jenkins-D24939?style=for-the-badge&logo=jenkins&logoColor=white)
![ArgoCD](https://img.shields.io/badge/ArgoCD-EF7B4D?style=for-the-badge&logo=argo&logoColor=white)
![Nginx](https://img.shields.io/badge/Nginx-009639?style=for-the-badge&logo=nginx&logoColor=white)
![GitLab](https://img.shields.io/badge/GitLab-FC6D26?style=for-the-badge&logo=gitlab&logoColor=white)
![React](https://img.shields.io/badge/React-61DAFB?style=for-the-badge&logo=react&logoColor=black)
![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=for-the-badge&logo=typescript&logoColor=white)

**A production-grade DevOps pipeline — from code commit to live Kubernetes deployment**

[🌐 Portfolio](https://Rokkamravi9676.github.io) · [💼 LinkedIn](https://www.linkedin.com/in/ravi-rokkam-aa0b9a1b7) · [📧 Contact](mailto:rokkamravi1999@gmail.com)

</div>

---

## 📋 Table of Contents

- [Project Overview](#-project-overview)
- [Architecture](#-architecture)
- [CI/CD Pipeline](#-cicd-pipeline)
- [Tech Stack](#-tech-stack)
- [Repository Structure](#-repository-structure)
- [Kubernetes Setup](#-kubernetes-setup)
- [Local Development](#-local-development)
- [Pipeline Flow](#-pipeline-flow)
- [Key DevOps Concepts Implemented](#-key-devops-concepts-implemented)
- [Author](#-author)

---

## 🚀 Project Overview

The **Payment Advice System** is a financial web application built with React + TypeScript, deployed on an **on-premise Kubernetes cluster** running on Proxmox VMs. This repository showcases a complete **end-to-end DevOps implementation** including:

- ✅ Containerization with **multi-stage Docker builds**
- ✅ Private image registry using **Harbor Hub**
- ✅ Automated CI/CD with **Jenkins declarative pipeline**
- ✅ **GitOps** deployment workflow with **ArgoCD**
- ✅ **Zero-downtime** rolling deployments on **Kubernetes**
- ✅ **Nginx** reverse proxy with SPA routing, gzip & security headers
- ✅ On-premise infrastructure on **Proxmox VMs**

---

## 🏗️ Architecture

```
Developer
    │
    │  git push origin main
    ▼
┌─────────────┐
│   GitLab    │  ──── Webhook ────►  Jenkins
│  Repository │                         │
└─────────────┘                         │
        ▲                               │  Stage 1: Clone
        │                               │  Stage 2: Docker Build
        │  git push                     │  Stage 3: Push to Harbor
        │  (manifest update)            │  Stage 4: Update kustomization.yaml
        │                               │
        └───────────────────────────────┘
                                        │
                                        ▼
                               ┌─────────────────┐
                               │   Harbor Hub     │
                               │ Private Registry │
                               └─────────────────┘
                                        │
                                        │  Image stored
                                        ▼
┌─────────────┐     Detects       ┌──────────┐
│   GitLab    │ ─── manifest ───► │  ArgoCD  │
│  k8s/ path  │     change        │  GitOps  │
└─────────────┘                   └──────────┘
                                        │
                                        │  kubectl apply
                                        ▼
                          ┌─────────────────────────┐
                          │      Kubernetes          │
                          │   Namespace: dev-p360    │
                          │                          │
                          │  ┌──────────────────┐   │
                          │  │   Deployment     │   │
                          │  │  (Rolling Update) │   │
                          │  └──────────────────┘   │
                          │  ┌────────┐ ┌────────┐  │
                          │  │ Pod 1  │ │ Pod 2  │  │
                          │  └────────┘ └────────┘  │
                          │  ┌──────────────────┐   │
                          │  │    Service        │   │
                          │  │  (NodePort :80)   │   │
                          │  └──────────────────┘   │
                          │  ┌──────────────────┐   │
                          │  │     Ingress       │   │
                          │  │  (Nginx Controller)│  │
                          │  └──────────────────┘   │
                          └─────────────────────────┘
                                        │
                                        ▼
                             http://172.16.30.60:30080
```

---

## 🔄 CI/CD Pipeline

The Jenkins pipeline has **4 automated stages**:

```
┌──────────────┐    ┌──────────────────┐    ┌──────────────┐    ┌─────────────────┐
│    Stage 1   │    │     Stage 2      │    │   Stage 3    │    │    Stage 4      │
│    Clone     │───►│  Build & Push    │───►│  Update      │───►│  ArgoCD Sync   │
│  Repository  │    │  Docker Image    │    │  GitOps Repo │    │  & Restart     │
└──────────────┘    └──────────────────┘    └──────────────┘    └─────────────────┘
     GitLab              Harbor Hub            kustomization        Kubernetes
   git clone           docker build            yaml update          rolling update
                        docker push             git push
```

### Stage Details

| Stage | Action | Tool |
|-------|--------|------|
| **Clone Repository** | Pull latest code from GitLab main branch | Jenkins + GitLab |
| **Build & Push** | Multi-stage Docker build → push to Harbor | Docker + Harbor |
| **Update GitOps Repo** | Update image tag in `k8s/kustomization.yaml` | Git + sed |
| **ArgoCD Sync** | Trigger sync → rolling update in Kubernetes | ArgoCD CLI |

---

## 🛠️ Tech Stack

| Category | Technology | Purpose |
|----------|-----------|---------|
| **Frontend** | React 19 + TypeScript + Vite | Web application |
| **Containerization** | Docker (multi-stage) | Build & package app |
| **Web Server** | Nginx 1.27 Alpine | Serve static assets |
| **Registry** | Harbor Hub | Private image storage |
| **CI/CD** | Jenkins | Pipeline automation |
| **GitOps** | ArgoCD | Kubernetes deployment |
| **Orchestration** | Kubernetes 1.28 | Container management |
| **Version Control** | GitLab | Source + GitOps repo |
| **Infrastructure** | Proxmox VMs (Ubuntu 22.04) | On-premise cluster |

---

## 📁 Repository Structure

```
payment-advice/
├── 📁 k8s/                          # Kubernetes manifests
│   ├── namespace.yaml               # dev-p360 namespace
│   ├── configmap.yaml               # Nginx config as ConfigMap
│   ├── deployment.yaml              # App deployment (rolling update)
│   ├── service.yaml                 # NodePort service
│   ├── ingress.yaml                 # Nginx Ingress Controller
│   ├── hpa.yaml                     # Horizontal Pod Autoscaler
│   ├── pdb.yaml                     # Pod Disruption Budget
│   └── kustomization.yaml           # Kustomize (image tag updated by Jenkins)
│
├── 📁 src/                          # React application source
│   ├── components/
│   ├── data/
│   └── ...
│
├── 📁 scripts/                      # Build scripts
│   └── import-excel.mjs             # Excel → JSON data converter
│
├── 📄 Dockerfile                    # Multi-stage production build
├── 📄 Dockerfile.dev                # Development build with HMR
├── 📄 docker-compose.yml            # Local development setup
├── 📄 nginx.conf                    # Nginx SPA configuration
├── 📄 Jenkinsfile                   # CI/CD pipeline definition
├── 📄 .dockerignore                 # Docker build exclusions
└── 📄 .env.example                  # Environment variables template
```

---

## ☸️ Kubernetes Setup

### Namespace & Resources

```bash
# Create namespace
kubectl create namespace dev-p360

# Create Harbor pull secret (one time)
kubectl create secret docker-registry harbor-pull-secret \
  --namespace dev-p360 \
  --docker-server=hub.p360.build \
  --docker-username=<username> \
  --docker-password=<password>

# Apply all manifests
kubectl apply -k k8s/

# Verify deployment
kubectl get pods -n dev-p360
kubectl get svc -n dev-p360
kubectl get ingress -n dev-p360
```

### Kubernetes Resources Created

| Resource | Name | Purpose |
|----------|------|---------|
| Namespace | `dev-p360` | Isolates all app resources |
| ConfigMap | `nginx-config` | Nginx config without rebuilding image |
| Deployment | `payment-advice-ui` | Manages pods with rolling update |
| Service | `payment-advice-ui` | NodePort exposes app on port 30080 |
| Ingress | `payment-advice-ui` | Routes traffic via Nginx controller |
| HPA | `payment-advice-ui` | Auto-scales 1-5 pods on CPU usage |
| PDB | `payment-advice-ui` | Ensures min 1 pod during disruptions |

---

## 💻 Local Development

### Prerequisites
- Node.js 20+
- Docker Desktop
- kubectl (configured to your cluster)

### Run with Docker Compose

```bash
# Clone the repo
git clone https://gitlab.p360.build/sudheer-projects/payment-advice.git
cd payment-advice

# Setup environment
cp .env.example .env

# Production mode (React build + Nginx)
docker compose up --build
# Access: http://localhost:3000

# Development mode (Vite HMR — live reload)
docker compose --profile dev up --build
# Access: http://localhost:5173

# Stop
docker compose down
```

### Run locally without Docker

```bash
npm install
npm run import-data    # Generate JSON from Excel
npm run dev            # Start Vite dev server
```

---

## 🔧 Pipeline Flow

### How a deployment works (step by step)

```
1. Developer pushes code to GitLab main branch
   └── git push origin main

2. GitLab webhook triggers Jenkins build

3. Jenkins Stage 1 — Clone
   └── git clone from GitLab

4. Jenkins Stage 2 — Build & Push
   └── docker build --no-cache -t hub.p360.build/p360/payment-advice:latest .
   └── docker push hub.p360.build/p360/payment-advice:latest

5. Jenkins Stage 3 — Update GitOps Manifest
   └── git clone repo
   └── sed update newTag in k8s/kustomization.yaml
   └── git commit -m "ci: update image [skip ci]"
   └── git push origin main

6. ArgoCD detects kustomization.yaml changed
   └── Runs: kustomize build k8s/
   └── Applies updated manifests to Kubernetes

7. Kubernetes performs rolling update
   └── New pod starts → readiness probe passes
   └── Old pod terminates
   └── Zero downtime ✅

8. App live at: http://172.16.30.60:30080
```

---

## 🎯 Key DevOps Concepts Implemented

| Concept | Implementation |
|---------|---------------|
| **Infrastructure as Code** | All K8s resources defined in YAML files in Git |
| **GitOps** | ArgoCD syncs cluster state from Git — Git is single source of truth |
| **Zero Downtime Deploy** | Rolling update with `maxUnavailable: 0` |
| **Immutable Infrastructure** | New image tag per build — never modify running containers |
| **Secrets Management** | Kubernetes secrets for registry auth — never stored in Git |
| **Health Checks** | Liveness + Readiness probes on `/health` endpoint |
| **Resource Limits** | CPU/memory requests and limits on all containers |
| **High Availability** | HPA auto-scales, PDB prevents full outage during maintenance |

---

## 👨‍💻 Author

<div align="center">

**Rokkam Ravi**
DevOps Engineer @ Piersoft Technologies
3+ Years Experience

[![Portfolio](https://img.shields.io/badge/Portfolio-000000?style=for-the-badge&logo=github&logoColor=white)](https://Rokkamravi9676.github.io)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/ravi-rokkam-aa0b9a1b7)
[![GitHub](https://img.shields.io/badge/GitHub-100000?style=for-the-badge&logo=github&logoColor=white)](https://github.com/Rokkamravi9676)
[![Email](https://img.shields.io/badge/Email-D14836?style=for-the-badge&logo=gmail&logoColor=white)](mailto:rokkamravi1999@gmail.com)

</div>

---

<div align="center">
<sub>Built with ❤️ using Docker · Kubernetes · Jenkins · ArgoCD · GitLab · Harbor · Nginx</sub>
</div>
