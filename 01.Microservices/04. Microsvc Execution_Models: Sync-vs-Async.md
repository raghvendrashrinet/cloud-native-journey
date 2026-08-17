## Microservice Execution Models: Sync vs. Async

When orchestrating microservice interactions, operations fall into two distinct execution patterns:

#### ⚡ Case 1: Synchronous Use Case (e.g., Add to Basket)
Synchronous workflows are blocking and request-driven. The client expects immediate verification before proceeding.
```
[ Client / Frontend ]
         │
         │ 1. Get latest discount
         ▼
┌──────────────────┐      HTTP / gRPC      ┌──────────────────┐
│  Basket Service  ├──────────────────────►│ Discount Service │
└────────┬─────────┘                       └──────────────────┘
         │
         │ 2. Add item to basket
         ▼
 ┌───────────────┐
 │ Redis Basket  │
 └───────────────┘
```

**Workflow Steps:**
- Get latest discount from discount: Queries the Discount service synchronously to apply real-time pricing rules.
- Add basket: Persists the item directly to high-speed session storage.


#### 📩 Case 2: Asynchronous Use Case (e.g., Place Order)
Asynchronous workflows are non-blocking and event-driven. Tasks are decoupled through event brokers to maintain system throughput and resilience.
```
[ Client ] ──( Place Order )──► [ Order Service ]
                                      │
                                      ▼ (Publishes: OrderPlaced)
                      ┌──────────────────────────────┐
                      │    Event Broker (Kafka/NATS) │
                      └──────────────┬───────────────┘
                                     │
         ┌───────────────────────────┼───────────────────────────┐
         ▼                           ▼                           ▼
┌─────────────────┐         ┌─────────────────┐         ┌─────────────────┐
│ Payment Service │         │ Inventory Serv. │         │ Shipping Serv.  │
├─────────────────┤         ├─────────────────┤         ├─────────────────┤
│ 1. Get payment  │         │ 2. Decrease     │         │ 3. Send         │
│                 │         │    stock        │         │    shipment     │
└────────┬────────┘         └─────────────────┘         └─────────────────┘
         │
         ▼
┌─────────────────┐         ┌─────────────────┐
│ Billing Service │         │ Order Service   │
├─────────────────┤         ├─────────────────┤
│ 4. Generate     │         │ 5. Update order │
│    bill         │         │    table        │
└─────────────────┘         └─────────────────┘
```
**Workflow Steps:**
1. `Order placed`: Triggers the checkout workflow by publishing an OrderPlaced event.
2. `Get payment`: Payment Service consumes event and charges the customer.
3. `Decrease stock`: Inventory Service reserves and decrements physical stock counts.
4. `Send shipment`: Shipping Service dispatches fulfillment and tracking requests.
5. `Generate bill`: Billing Service creates an invoice and issues payment receipts.
6. `Update order table`: Order status transitions to COMPLETED once all async steps succeed.

#### 🏛️ 3. Object-Responsibility Mapping (CRC Model)
```
┌────────────────────────────────────────────────────────────────────────┐
│ Service: Order Service                                                 │
├──────────────────────────────────────┬─────────────────────────────────┤
│ Responsibilities (What it does)      │ Collaborators (Who it talks to) │
├──────────────────────────────────────┼─────────────────────────────────┤
│ • Validate checkout payload          │ • Basket Service (fetch items)  │
│ • Publish OrderPlaced event          │ • Payment Service (async event) │
│ • Track final order status lifecycle │ • Inventory Service (async)     │
└──────────────────────────────────────┴─────────────────────────────────┘
```

| Attribute                  | Synchronous (Sync)                          | Asynchronous (Async)                          |
|-----------------------------|---------------------------------------------|-----------------------------------------------|
| Communication Protocol      | REST, gRPC, GraphQL                         | Kafka, NATS, RabbitMQ                         |
| Coupling                    | High (Tight temporal coupling)              | Low (Decoupled producers/consumers)           |
| Latency & User Experience   | Fast immediate feedback required            | Eventual consistency; background processing   |
| Failure Handling            | Retries, Circuit Breakers                   | Dead Letter Queues (DLQ), Saga Pattern        |
