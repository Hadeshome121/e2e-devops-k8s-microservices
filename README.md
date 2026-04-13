# 🚀 E2E DevOps Microservices Pipeline with Kubernetes

> A complete end-to-end DevOps automation pipeline for deploying microservices locally using Terraform, Ansible, Docker, Kubernetes (Minikube), Helm, and GitHub Actions.

---

## 📋 Table of Contents

- [Overview](#overview)
- [Tech Stack](#tech-stack)
- [Architecture](#architecture)
- [Repository Structure](#repository-structure)
- [Prerequisites](#prerequisites)
- [Getting Started](#getting-started)
  - [1. Infrastructure Setup with Terraform](#1-infrastructure-setup-with-terraform)
  - [2. Environment Configuration with Ansible](#2-environment-configuration-with-ansible)
  - [3. Deploy Microservices with Helm](#3-deploy-microservices-with-helm)
  - [4. Access the Services](#4-access-the-services)
  - [5. Setup GitHub Actions CI/CD](#5-setup-github-actions-cicd)
- [Features](#features)
- [Contributing](#contributing)
- [License](#license)

---

## Overview

This project provides a fully automated, production-grade DevOps pipeline for deploying a microservices application to a **local Kubernetes cluster (Minikube)**. It demonstrates real-world GitOps practices — from infrastructure provisioning with Terraform, to service configuration with Ansible, containerization with Docker, orchestration with Kubernetes, and continuous deployment via GitHub Actions.

---

## Tech Stack

### Infrastructure & Orchestration

| Tool | Purpose |
|------|---------|
| **Terraform** | Infrastructure provisioning (IaC) |
| **Ansible** | Configure Docker, Kubernetes & Helm (CaC) |
| **Docker** | Container runtime |
| **Kubernetes (Minikube)** | Local Kubernetes cluster |
| **Helm** | Package manager for Kubernetes |

### Microservices

| Service | Technology | Role |
|---------|------------|------|
| Backend API | Node.js | REST API server |
| Frontend UI | ReactJS | Web interface |
| Database | MySQL | Persistent data storage |
| Cache | Redis | In-memory caching layer |
| Gateway | Ingress (Nginx) | Centralized routing |

### CI/CD

| Tool | Purpose |
|------|---------|
| **GitHub Actions** | Automated CI/CD pipeline |
| **Self-hosted Runners** | Runs pipelines inside Kubernetes or a dedicated VM |

---

## Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    GitHub Actions CI/CD                  │
│         Build → Test → Push Image → Helm Upgrade        │
└────────────────────────┬────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────┐
│                 Local Kubernetes (Minikube)              │
│                                                         │
│   ┌──────────┐    ┌──────────┐    ┌──────────────────┐  │
│   │  ReactJS │    │  Node.js │    │  Ingress Gateway │  │
│   │  (Front) │◄──►│   (API)  │◄──►│  (gateway-chart) │  │
│   └──────────┘    └──────────┘    └──────────────────┘  │
│                        │                                 │
│              ┌─────────┴──────────┐                     │
│              │                    │                      │
│         ┌────▼─────┐       ┌──────▼────┐                │
│         │  MySQL   │       │   Redis   │                 │
│         │   (DB)   │       │  (Cache)  │                 │
│         └──────────┘       └───────────┘                 │
└─────────────────────────────────────────────────────────┘
         ▲
         │
┌────────┴────────────────────────────────────────────────┐
│            Terraform → Ansible → Docker/K8s/Helm        │
└─────────────────────────────────────────────────────────┘
```

---

## Repository Structure

```
e2e-devops-k8s-microservices-pipeline/
├── terraform/                  # Infrastructure provisioning scripts
├── ansible/                    # Ansible playbooks
│   ├── setup-docker.yml        # Install & configure Docker
│   ├── setup-k8s.yml           # Setup Kubernetes (Minikube)
│   └── setup-helm.yml          # Install Helm
├── k8s-helm/
│   ├── nodejs-chart/           # Helm chart — Node.js backend API
│   ├── reactjs-chart/          # Helm chart — ReactJS frontend
│   ├── mysql-chart/            # Helm chart — MySQL database
│   ├── redis-chart/            # Helm chart — Redis cache
│   └── gateway-chart/          # Helm chart — Ingress gateway
├── .github/
│   └── workflows/              # GitHub Actions CI/CD pipelines
└── README.md
```

---

## Prerequisites

Make sure the following tools are installed and running on your machine before getting started:

- [ ] [Docker](https://docs.docker.com/get-docker/) — installed and running
- [ ] [Minikube](https://minikube.sigs.k8s.io/docs/start/) — installed
- [ ] [kubectl](https://kubernetes.io/docs/tasks/tools/) — configured
- [ ] [Helm](https://helm.sh/docs/intro/install/) v3+
- [ ] [Terraform](https://developer.hashicorp.com/terraform/install) — installed
- [ ] [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/index.html) — installed
- [ ] A GitHub repository with Actions enabled

---

## Getting Started

### 1. Infrastructure Setup with Terraform

Initialize and apply the Terraform configuration to provision your local infrastructure:

```bash
cd terraform
terraform init
terraform apply
```

### 2. Environment Configuration with Ansible

Run the Ansible playbooks to install and configure Docker, Kubernetes, and Helm:

```bash
cd ansible

# Install and configure Docker
ansible-playbook setup-docker.yml

# Setup Minikube / Kubernetes
ansible-playbook setup-k8s.yml

# Install Helm
ansible-playbook setup-helm.yml
```

### 3. Deploy Microservices with Helm

**Add required Helm repositories:**

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
```

**Create a dedicated namespace and deploy all services:**

```bash
kubectl create namespace devops

helm install mysql-release        ./k8s-helm/mysql-chart    -n devops
helm install redis-release        ./k8s-helm/redis-chart    -n devops
helm install main-service-release ./k8s-helm/nodejs-chart   -n devops
helm install reactjs-release      ./k8s-helm/reactjs-chart  -n devops
helm install gateway-release      ./k8s-helm/gateway-chart  -n devops
```

**Verify all pods are running:**

```bash
kubectl get pods -n devops
```

### 4. Access the Services

Add the following entries to your `/etc/hosts` file to enable local domain routing:

```
127.0.0.1   reactjs.local
127.0.0.1   api.local
```

| Service | URL |
|---------|-----|
| **Frontend (ReactJS)** | https://reactjs.local |
| **Backend API (Node.js)** | http://api.local |

> **Note:** The live demo frontend is also available at [https://deluxe-llama-e6c608.netlify.app](https://deluxe-llama-e6c608.netlify.app)

### 5. Setup GitHub Actions CI/CD

1. **Register a self-hosted runner** inside your Kubernetes cluster or on a dedicated VM. Follow the [GitHub self-hosted runner guide](https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners/adding-self-hosted-runners).

2. **Workflows** in `.github/workflows/` will automatically:
   - Build Docker images on push
   - Push images to Docker Hub (or your private registry)
   - Deploy updated services to Kubernetes using `helm upgrade`

---

## Features

- ✅ **Full local microservices deployment** — all services run inside Minikube
- ✅ **GitOps-friendly** — Helm-based deployments driven by Git changes
- ✅ **Centralized Ingress gateway** — single entry point via `gateway-chart`
- ✅ **Modular, reusable Helm charts** — each service is independently configurable
- ✅ **Infrastructure-as-Code (IaC)** — Terraform manages infrastructure state
- ✅ **Configuration-as-Code (CaC)** — Ansible ensures reproducible environment setup
- ✅ **Automated CI/CD** — GitHub Actions pipelines with self-hosted runners
- ✅ **Namespace isolation** — all services scoped to the `devops` namespace

---

## Contributing

Contributions are welcome! Feel free to open an issue or submit a pull request.

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/your-feature`
3. Commit your changes: `git commit -m 'Add your feature'`
4. Push to the branch: `git push origin feature/your-feature`
5. Open a Pull Request

---

## License

This project is licensed under the **MIT License**. See the [LICENSE](LICENSE) file for details.
