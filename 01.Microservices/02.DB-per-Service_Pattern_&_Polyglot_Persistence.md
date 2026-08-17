# 🗄️ Database-per-Service Pattern & Polyglot Persistence

The **Database-per-Service** pattern is a core architectural pattern in microservices where each service manages and owns its data privately. No other service can access another service's database directly.

---

## 📌 Core Characteristics

- **Loose Coupling:** Services remain completely independent. Changes to one service's database schema do not break or impact other microservices.
- **Encapsulation:** External services must access data strictly through public REST, gRPC, or event-based APIs.
- **Polyglot Persistence:** Each microservice can choose the database engine (Relational, Document, Key-Value, Graph) that best fits its specific domain requirements.

---

## 🛍️ Example: E-Commerce Architecture

In an e-commerce platform, each domain service uses a specialized database engine tailored to its needs:

```text
┌─────────────────────────┐      ┌─────────────────────────┐      ┌─────────────────────────┐
│     Product Service     │      │     Ordering Service    │      │ Shopping Cart Service   │
└────────────┬────────────┘      └────────────┬────────────┘      └────────────┬────────────┘
             │                                │                                │
             ▼                                ▼                                ▼
┌─────────────────────────┐      ┌─────────────────────────┐      ┌─────────────────────────┐
│     MongoDB / DynamoDB  │      │ PostgreSQL / CockroachDB│      │       Redis Cache       │
│    (Catalog Document)   │      │   (ACID Transactions)   │      │   (In-Memory Key-Value) │
└─────────────────────────┘      └─────────────────────────┘      └─────────────────────────┘
```
 - Product Service: Uses a Document Store (e.g., MongoDB, DynamoDB)
 - Ordering Service: Uses a Relational DB (e.g., PostgreSQL, CockroachDB)
 - Shopping Cart Service: Uses an In-Memory Key-Value Store (e.g., Redis)
   
