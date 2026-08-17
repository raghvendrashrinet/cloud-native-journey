### Our Use Case for E com project 

```
┌─────────────────────────────────────────────────────────────────────────┐
│                          CLIENT APPS LAYER                              │
│                                                                         │
│         ┌───────────────┐                       ┌────────────────┐      │
│         │   Web App     │                       │   Mobile App   │      │
│         └───────┬───────┘                       └───────┬────────┘      │
└─────────────────┼───────────────────────────────────────┼───────────────┘
                  │                                       │
                  └───────────────────┬───────────────────┘
                                      │
                                      ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                            API GATEWAY                                  │
│         (Single Entry Point / Ingress Routing / Load Balancing)         │
└─────────────────────────────────────┬───────────────────────────────────┘
                                      │
         ┌────────────────────────────┼────────────────────────────┐
         ▼                            ▼                            ▼
┌──────────────────┐        ┌──────────────────┐        ┌──────────────────┐
│ Catalog Service  │        │Shopping Cart Serv│        │ Discount Service │
│  ┌────────────┐  │        │  ┌────────────┐  │        │  ┌────────────┐  │
│  │ DB (Mongo) │  │        │  │ DB (Redis) │  │        │  │ DB (Postgre│  │
│  └────────────┘  │        │  └────────────┘  │        │  └────────────┘  │
└────────┬─────────┘        └────────┬─────────┘        └────────┬─────────┘
         │                           │                           │
         └───────────────────────────┼───────────────────────────┘
                                     │
                                     ▼
┌──────────────────┐        ┌──────────────────────────────────────────────┐
│ Ordering Service │        │                MESSAGE BROKER                │
│  ┌────────────┐  │        │            (Kafka / RabbitMQ)                │
│  │ DB (SQL)   ├───────►   │   ┌──────────────────────────────────────┐   │
│  └────────────┘  │        │   │    Event Stream (OrderPlaced, etc.)  │   │
└──────────────────┘        │   └──────────────────────────────────────┘   │
                            └──────────────────────────────────────────────┘
```

#### 🏛️ Core Architecture Components
  1. `Client Apps (Web & Mobile)`: Access the system through decoupled frontend clients without direct access to backend microservice databases.
  2. `API Gateway`: Acts as the central reverse proxy and entry point. It handles routing, authentication, rate limiting, and SSL termination before forwarding requests to internal services.
  3. `Database-per-Service`: Each microservice strictly owns its database to ensure loose coupling, data isolation, and polyglot persistence:
  4. `Catalog Service DB`: Document-oriented DB (e.g., MongoDB) for storing dynamic product catalogs.
  5. `Shopping Cart Service DB`: In-memory key-value store (e.g., Redis) for fast session reads and updates.
  6. `Discount Service DB`: Relational or key-value store handling coupon codes and pricing rules.
  7. `Ordering` Service DB: Relational DB (e.g., PostgreSQL) ensuring ACID compliance for order processing.
  8. `Message Broker`: Asynchronous event spine (e.g., Kafka or RabbitMQ) used by services like Ordering to publish domain events (like OrderPlaced), allowing downstream services to process inventory, billing, and notifications asynchronously.
