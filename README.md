# E2E DevOps Microservices Pipeline with Kubernetes

This project is a complete end-to-end (E2E) DevOps automation pipeline for deploying microservices using modern DevOps tools. It sets up a local Kubernetes environment using Terraform, Ansible, Docker, and Kubernetes (k8s), deploys services using Helm charts, and automates CI/CD with GitHub Actions using self-hosted runners.

---

## Tech Stack

### Infrastructure and Orchestration

* Terraform: Automate infrastructure provisioning
* Ansible: Configure Docker, Kubernetes, and Helm locally
* Docker: Container runtime
* Kubernetes (Minikube): Local Kubernetes cluster
* Helm: Deploy microservices as charts

### Microservices

* Node.js: Backend API service
* ReactJS: Frontend UI
* MySQL: Database service
* Redis: Cache layer

### CI/CD

* GitHub Actions: CI/CD pipeline
* Self-hosted Runners: Run your pipelines inside your Kubernetes cluster or on a dedicated VM/container

---

## Repository Structure

```
e2e-devops-k8s-microservices-pipeline/
|-- terraform/            # Infrastructure provisioning scripts
|-- ansible/              # Ansible playbooks to setup Docker, Kubernetes, Helm
|-- k8s-helm/
|   |-- nodejs-chart/     # Helm chart for Node.js service
|   |-- reactjs-chart/    # Helm chart for ReactJS frontend
|   |-- mysql-chart/      # Helm chart for MySQL DB
|   |-- redis-chart/      # Helm chart for Redis cache
|   +-- gateway-chart/    # Helm chart for Ingress (gateway)
|-- .github/workflows/    # GitHub Actions workflows
+-- README.md             # This file
```

---

## Setup Instructions

### 1. Prerequisites

* Docker installed and running
* Minikube installed
* kubectl configured
* Helm installed
* Terraform and Ansible installed
* GitHub repository with Actions workflows

---

### 2. Infrastructure Setup

#### a. Initialize the Infrastructure with Terraform

```bash
cd terraform
terraform init
terraform apply
```

#### b. Configure Docker and Kubernetes with Ansible

```bash
cd ansible
ansible-playbook setup-docker.yml
ansible-playbook setup-k8s.yml
ansible-playbook setup-helm.yml
```

---

### 3. Deploy Services with Helm

#### a. Add Helm dependencies

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
```

#### b. Deploy microservices

```bash
kubectl create namespace devops

helm install mysql-release ./k8s-helm/mysql-chart -n devops
helm install redis-release ./k8s-helm/redis-chart -n devops
helm install main-service-release ./k8s-helm/nodejs-chart -n devops
helm install reactjs-release ./k8s-helm/reactjs-chart -n devops
helm install gateway-release ./k8s-helm/gateway-chart -n devops
```

---

### 4. Access the Services

* Frontend: http://reactjs.local/
* API: http://api.local/
* Edit /etc/hosts:

  ```
  127.0.0.1 reactjs.local
  127.0.0.1 api.local
  ```

---

### 5. Setup GitHub Actions CI/CD

* Register your Kubernetes pod or VM as a self-hosted runner.
* GitHub Actions workflows (.github/workflows/) will:

  * Build Docker images
  * Push to Docker Hub or your private registry
  * Deploy new versions using Helm upgrades

---

## DevOps Tools Flow

Terraform -> Ansible -> Docker/K8s/Helm -> GitHub Actions -> Helm Deployments -> Local Kubernetes (Minikube)

---

## Features

* Full local microservices deployment pipeline
* GitOps-friendly approach using Helm
* Centralized Ingress gateway (gateway-chart)
* Modular, reusable Helm charts
* Infrastructure-as-Code (IaC) and Configuration-as-Code (CaC)
* Simple self-hosted GitHub Actions runners for CI/CD automation

---

## License

MIT License

---

## Maintainer

Anandita Nagpal
Computer Science Engineer | Full-Stack and AI/GenAI Developer
Email: nagpalanandita@gmail.com
GitHub: github.com/Anandita-Nagpal
LinkedIn: linkedin.com/in/anandita-nagpal

Anandita is a Computer Science Engineer with over 2 years of professional experience building full-stack products. She focuses on React/Next.js, Node/FastAPI, and PostgreSQL, shipping pragmatic, production-ready features integrated with ML/GenAI, CI/CD, Docker, and cloud-native deployments.