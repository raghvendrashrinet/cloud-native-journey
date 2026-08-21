# Cloud-Native Architecture: Communication Strategy Guide

### 1. North-South Traffic (Client to Backend)
* **API Gateway Pattern:** Unified entry point (Kong, NGINX, AWS/Azure API Gateway) handling SSL termination, authentication, and rate limiting.
* **Protocols:** **REST/JSON** or **GraphQL** for client-facing responsiveness.
* **BFF Pattern:** Backend-For-Frontend proxies tailored for Web vs. Mobile optimizations.

### 2. East-West Traffic (Internal Microservices)

#### Synchronous (Request-Response)
* **gRPC (HTTP/2 + Protobuf):** Low-latency, strongly typed binary RPC for immediate inter-service dependencies.
* **Service Mesh (Istio / Envoy):** Enforces mutual TLS (mTLS), dynamic traffic routing, and distributed tracing via sidecars.

#### Asynchronous (Event-Driven)
* **Message Brokers:** **RabbitMQ** or **Apache Kafka** for non-blocking workflows.
* **Pub/Sub Pattern:** Services publish events (e.g., `OrderCreated`) to decouple background processes like notifications and inventory.

---

### Communication Matrix

| Flow | Protocol / Tool | Key Purpose |
| :--- | :--- | :--- |
| **Frontend → Backend** | HTTP/REST, GraphQL, API Gateway | Security, rate limiting, single entry point |
| **Service → Service (Sync)** | gRPC, Istio Service Mesh | High performance, mTLS encryption, internal calls |
| **Service → Service (Async)**| AMQP, RabbitMQ, Kafka | Decoupled events, background jobs, eventual consistency |
