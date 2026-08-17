
## E-Commerce Domain Analysis & Microservice Decomposition

### 🧩 Microservice Decomposition & Technical Design
🏛️ Domain Analysis & Bounded Contexts
- An e-commerce system naturally splits into distinct business capabilities:
- Product Catalog Management: Managing product details, categories, variants, pricing, and stock availability.
- Order Processing: Managing customer checkout, order creation, state transitions, and order history.
- Shopping Cart & Sessions: Managing active, temporary user shopping carts and real-time item updates.
- Payment & Billing: Interfacing with third-party payment gateways, issuing invoices, and handling refunds.
- Inventory & Warehouse: Tracking physical stock levels, reservation holds during checkout, and fulfillment.
- Customer / User Management: User profiles, authentication, authorization, and address books.
- Notification & Communication: Sending transactional emails, SMS, and order status push notifications.
```
                                  ┌───────────────────────────┐
                                  │  API Gateway (Kong/Ocelot)│
                                  └─────────────┬─────────────┘
                                                │
         ┌───────────────────┬──────────────────┼───────────────────┬───────────────────┐
         ▼                   ▼                  ▼                   ▼                   ▼
┌─────────────────┐ ┌─────────────────┐ ┌───────────────┐ ┌─────────────────┐ ┌─────────────────┐
│ Catalog Service │ │  Order Service  │ │ Basket Service│ │ Payment Service │ │Inventory Service│
└────────┬────────┘ └────────┬────────┘ └───────┬───────┘ └────────┬────────┘ └────────┬────────┘
         │                   │                  │                  │                   │
         ▼                   ▼                  ▼                  ▼                   ▼
┌─────────────────┐ ┌─────────────────┐ ┌───────────────┐ ┌─────────────────┐ ┌─────────────────┐
│ MongoDB / Mongo │ │PostgreSQL / ACID│ │ Redis Cache   │ │ Stripe / PayPal │ │ PostgreSQL / DB │
└─────────────────┘ └─────────────────┘ └───────────────┘ └─────────────────┘ └─────────────────┘
```
| User Story / Requirement                                               | Extracted Nouns (Entities)                  | Extracted Verbs (Actions)            | Target Microservice / Boundary   |
|------------------------------------------------------------------------|---------------------------------------------|--------------------------------------|----------------------------------|
| As a user, I want to list `products` and` filter `products by brand.        | Product, Brand, Category                     | list, filter                         | Catalog Service                  |
| As a user, I want to put products in the `shopping cart`.                 | Shopping Cart, Cart Item                     | put, add, update quantity            | Basket / Cart Service             |
| As a user, I want to specify `credit card` information during checkout.  | Credit Card, Payment, Checkout               | specify, charge, process             | Payment Service                   |
| As a user, I want to receive order `confirmation email`.                  | Email, Order Number, Confirmation            | receive, send                        | Notification Service              |
| As a user, I want system to tell me how many items are in `stock`.        | Stock, Item Quantity, Inventory              | tell, check, reserve                 | Inventory Service                 |

---
### 🏛️ 2. Object-Responsibility Diagram (CRC Card Model)
Object-Responsibility Mapping defines what a service knows, what it does, and which external services it depends on (Collaborators).

<img width="782" height="669" alt="image" src="https://github.com/user-attachments/assets/e502df4c-2c1e-4ab6-b9cb-716dd524c962" />

```
┌────────────────────────────────────────────────────────────────────────┐
│ Service: Order Service                                                 │
├──────────────────────────────────────┬─────────────────────────────────┤
│ Responsibilities (What it does)      │ Collaborators (Who it talks to) │
├──────────────────────────────────────┼─────────────────────────────────┤
│ • Validate order request & items     │ • Catalog Service (pricing)     │
│ • Coordinate checkout workflow       │ • Inventory Service (hold stock)│
│ • Persist order state transitions    │ • Payment Service (process pay) │
│ • Publish OrderPlaced domain event   │ • Notification Service (email)  │
└──────────────────────────────────────┴─────────────────────────────────┘
┌────────────────────────────────────────────────────────────────────────┐
│ Service: Inventory Service                                             │
├──────────────────────────────────────┬─────────────────────────────────┤
│ Responsibilities (What it does)      │ Collaborators (Who it talks to) │
├──────────────────────────────────────┼─────────────────────────────────┤
│ • Maintain real-time stock counts    │ • Order Service (receives events│
│ • Manage inventory hold reservations │ • Warehouse API (stock updates) │
│ • Release stock upon cancellation    │                                 │
└──────────────────────────────────────┴─────────────────────────────────┘
```
### 🧩 3. End-to-End Decomposition Workflow
```
┌─────────────────────────────────┐
│     Business Requirements       │
└────────────────┬────────────────┘
                 │
                 ▼
┌─────────────────────────────────┐
│     Nouns & Verbs Analysis      │  <-- Extracts Entities & Operations
└────────────────┬────────────────┘
                 │
                 ▼
┌─────────────────────────────────┐
│    Object-Responsibility Maps   │  <-- Establishes CRC Responsibilities & Dependencies
└────────────────┬────────────────┘
                 │
                 ▼
┌─────────────────────────────────┐
│  Bounded Contexts & Domain DBs  │  <-- Defines Microservices & Polyglot Persistence
└─────────────────────────────────┘
```
### 🛠️ 4. Microservice Target Architecture Summary


| Microservice       | Extracted Nouns                  | Main Operations (Verbs)          | Recommended Data Store            | Communication             |
|--------------------|----------------------------------|----------------------------------|-----------------------------------|---------------------------|
| Catalog Service    | Product, Category, Brand         | Search, Filter, Browse           | MongoDB / Elasticsearch           | REST / gRPC               |
| Basket Service     | Cart, Session, Item              | Add, Modify, Clear               | Redis In-Memory                   | REST / Redis API          |
| Order Service      | Order, Line Item, Checkout       | Create, Cancel, Fulfill          | PostgreSQL / CockroachDB          | gRPC / Kafka Events       |
| Payment Service    | Payment, Transaction, Refund     | Charge, Authorize, Refund        | PostgreSQL                        | gRPC / Webhooks           |
| Inventory Service  | Stock, SKU, Warehouse            | Reserve, Deduct, Release         | PostgreSQL                        | AMQP / RabbitMQ           |
