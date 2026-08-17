# Cloud-Native Knowledge Base & Hands-On Journey

Welcome to my personal knowledge base and progress tracker for mastering Cloud-Native Architecture, Kubernetes, Microservices, and DevOps/CI-CD workflows. 

This repository serves as a centralized record of concepts, hands-on lab projects, architectural patterns, and configurations built while completing the course **[Cloud-Native: Microservices, Kubernetes, Service Mesh, CI/CD](https://samsungu.udemy.com/course/cloud-native-microservices-kubernetes-service-mesh-cicd/learn/lecture/38718992#overview)** by **[Mehmet Ozkaya](https://samsungu.udemy.com/course/cloud-native-microservices-kubernetes-service-mesh-cicd/learn/lecture/38718992#overview)**.

---

## 🗺️ Cloud-Native Pillars Architecture Map
```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│  Microservices  │ ──► │   Containers    │ ──► │  Orchestrators  │ ──► │ Communications  │
└────────┬────────┘     └────────┬────────┘     └────────┬────────┘     └────────┬────────┘
         │                       │                       │                       │
• Spring Boot           • Docker                • Kubernetes            • Service Meshes
• NodeJS                • Containerd            • Docker Swarm          • Istio
• .NET                  • CRI-O                 • OpenShift             • Linkerd
• Go Micro                                                                       │
                                                                                 ▼
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│  Monitoring &   │ ◄── │  DevOps, CI/CD  │ ◄── │   Scalability   │ ◄── │ Backing Services│
│  Observability  │     │    & GitOps     │     │                 │     │                 │
└────────┬────────┘     └────────┬────────┘     └────────┬────────┘     └────────┬────────┘
│                       │                       │                       │
• Prometheus            • GitHub Actions        • HPA                   • K8s Databases
• Grafana               • ArgoCD                • KEDA                  • Distributed Caches
• ELK Stack             • Flux                  • Serverless            • Message Brokers
```
<img width="1024" height="559" alt="image" src="https://github.com/user-attachments/assets/ad557d33-cab8-4420-b931-01d32e41fc07" />

---

## 🎯 Learning Objectives

- Master core **Cloud-Native Pillars**: Microservices, Containers, Orchestrators, Communications, Backing Services, Scalability, DevOps/CI-CD, and Monitoring.
- Gain practical experience designing RESTful microservices, containerizing with Docker, and orchestrating deployments using Kubernetes & Helm.
- Implement advanced service mesh communications using Istio and Envoy.
- Provision infra using Terraform and automate build/release pipelines with GitHub Actions, GitOps (ArgoCD/Flux), and Kubernetes HPA/KEDA autoscaling.
- Set up cluster observability with Prometheus, Grafana, and the ELK stack.

---

## 🚦 Roadmap & Progress Tracker

- [x] **1. Microservices** — Architecture design, domain boundaries, RESTful API design (`.NET`, `Spring Boot`, `Node.js`, `Go Micro`).
- [ ] **2. Containers** — Containerization patterns, multi-stage builds (`Docker`, `containerd`, `CRI-O`).
- [ ] **3. Orchestrators** — Cluster management, primitives, Helm charts (`Kubernetes`, `Docker Swarm`, `OpenShift`).
- [ ] **4. Communications** — Inter-pod networking, traffic management (`Istio`, `Linkerd`, `Envoy`, `gRPC`).
- [ ] **5. Backing Services** — Data persistence, distributed caching, and event-driven streaming (`CockroachDB`, `Redis`, `Apache Kafka`).
- [ ] **6. Scalability** — Autoscaling workloads and event-driven pod execution (`HPA`, `KEDA`, Serverless).
- [ ] **7. DevOps, CI/CD & GitOps** — Infrastructure as Code and declarative continuous delivery (`Terraform`, `GitHub Actions`, `ArgoCD`, `Flux`).
- [ ] **8. Monitoring & Observability** — Telemetry, metrics collection, and distributed logging (`Prometheus`, `Grafana`, `ELK Stack`).

---

## 📁 Repository Structure

```text
.
├── 01-microservices/       # Microservice source code, design patterns, API specs
├── 02-containers/          # Dockerfiles, multi-stage builds, container configs
├── 03-kubernetes/          # K8s manifests, Helm charts, Minikube / EKS deployments
├── 04-service-mesh/        # Istio & Envoy configurations
├── 05-backing-services/    # CockroachDB, Redis, and Kafka manifest configurations
├── 06-scalability/         # HPA & KEDA autoscaling rules and load test scripts
├── 07-devops-cicd/         # Terraform IaC files, GitHub Actions workflows, GitOps manifests
├── 08-observability/       # Prometheus configs, Grafana dashboards, ELK stack setups
└── notes/                  # Pillar summaries, conceptual cheat sheets, key takeaways
