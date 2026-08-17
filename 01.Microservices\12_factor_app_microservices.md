# 📄 The 12-Factor App Methodology for Microservices

The **12-Factor App** methodology provides a declarative set of best practices for building cloud-native, scalable, and resilient SaaS applications and microservices.

---
<img width="1024" height="559" alt="image" src="https://github.com/user-attachments/assets/3254ac4d-2dee-4d6e-8798-bc579994a1be" />

---
## 📌 Core Factors in Microservices Context

### 1. Codebase
* **Principle:** One codebase tracked in version control (Git), deployed into multiple environments (Dev, Staging, Prod).
* **Microservices Application:** Each microservice maintains its **own separate codebase/repository**, allowing independent deployments, scalability, and isolation.

### 2. Dependencies
* **Principle:** Explicitly declare and isolate all software dependencies.
* **Microservices Application:** Never rely on system-wide libraries on the host machine. Use package managers (e.g., `npm`, `NuGet`, `Maven`, `Go Modules`) and containerize using Docker to isolate dependencies per service.

### 3. Configuration
* **Principle:** Store application configurations in the runtime environment, completely separate from source code.
* **Microservices Application:** Use environment variables, Kubernetes ConfigMaps, or Secrets so that microservice binaries remain environment-agnostic across Dev, Staging, and Production.

### 4. Backing Services
* **Principle:** Treat external resources (databases, message brokers, caching nodes) as attached network resources.
* **Microservices Application:** Attach databases (e.g., PostgreSQL, CockroachDB) and caches (e.g., Redis) via connection URIs without tightly coupling code to local services.

### 5. Build, Release, Run
* **Principle:** Strictly separate the deployment pipeline into three distinct stages: Build (compilation), Release (code + config), and Run (execution).
* **Microservices Application:** Automate build pipelines using GitHub Actions or ArgoCD to build immutable Docker images tagged by version.

### 6. Processes
* **Principle:** Execute the application as one or more stateless, share-nothing processes.
* **Microservices Application:** Store session or transaction data in external backing services (e.g., Redis) so microservice pods can restart or scale up/down freely without losing state.

### 7. Port Binding
* **Principle:** Export services by binding directly to a network port.
* **Microservices Application:** Expose HTTP/gRPC endpoints directly from web servers embedded in code (e.g., ASP.NET Core Kestrel, Node Express, Spring Boot) rather than deploying into separate host web servers.

### 8. Concurrency
* **Principle:** Scale out horizontally through process multiplication.
* **Microservices Application:** Scale workloads by adding more pods in Kubernetes using Horizontal Pod Autoscaler (HPA) rather than resizing host virtual machines.

### 9. Disposability
* **Principle:** Maximize robustness with fast startup and graceful shutdown routines.
* **Microservices Application:** Design microservices to handle instant container deletion or pod crashes seamlessly, releasing database connections and flushing queue items safely.

### 10. Development/Production Parity
* **Principle:** Keep development, staging, and production environments as similar as possible.
* **Microservices Application:** Use identical container images, Kubernetes manifests, and backing services across all stages to avoid "works on my machine" issues.

### 11. Logs
* **Principle:** Treat log outputs as continuous event streams.
* **Microservices Application:** Output logs directly to standard streams (`stdout`/`stderr`) and route them to central aggregators like Prometheus, Grafana, or the ELK Stack.

### 12. Admin Processes
* **Principle:** Execute administrative or maintenance tasks (such as database migrations) as one-off tasks in the same environment as app code.
* **Microservices Application:** Run database migrations or batch jobs as dedicated Kubernetes Jobs using identical container images and configurations.
