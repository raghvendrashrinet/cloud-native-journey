# KB: Multi-Region Database Architectures

Quick reference guide comparing traditional relational databases (PostgreSQL/MySQL) against native distributed databases (CockroachDB) in multi-region environments.

---

## 1. Traditional RDBMS (PostgreSQL / MySQL)
Because they were designed for single-node operations, multi-region capabilities must be layered on top via replication engines.

### Pattern A: Primary-Replica (Active-Passive)
* **Architecture:** One "Primary" region handles all writes. Secondary regions hold read-only copies.  
* **Sync Mechanism:** Asynchronous Streaming Replication (Postgres) or Binary Logs (MySQL).  
* **Trade-off:** High write latency for distant users; risk of "Read-After-Write" lag while replicas catch up.

### Pattern B: Multi-Primary (Active-Active)
* **Architecture:** Every region accepts both reads and writes simultaneously.  
* **Sync Mechanism:** Bi-directional Logical Replication (Postgres native 16+ or extensions like pgEdge/EDB BDR; MySQL Group Replication).  
* **Trade-off:** Extremely high architectural complexity. Requires application-side conflict resolution and alternative keys (like UUIDs instead of auto-incrementing IDs).

### Pattern C: Cloud-Managed (e.g., AWS Aurora Global Database)
* **Architecture:** Decouples compute from storage.  
* **Sync Mechanism:** Storage-level asynchronous replication bypassing the database engine.  
* **Trade-off:** Fast replication lag (< 1 sec) and low maintenance, but locks you into a specific cloud provider.

---

## 2. Distributed Cloud-Native (CockroachDB)
Built from the ground up to handle multi-region sharding natively behind a standard SQL interface.

### Core Architectural Pillars
* **Consensus Replication (Raft):** Tables are split into 64MB "Ranges." Every range is copied across ≥3 regions. Writes only need a majority quorum to succeed. Yields automatic failover with zero data loss (RPO = 0).  
* **Localized Data Placement:** Data residency rules configured directly in SQL.  
  - Regional Tables: Data pinned to local servers for sub-millisecond local performance.  
  - Regional-by-Row: Shards rows into target countries based on columns (critical for GDPR compliance).  
  - Global Tables: Optimized for fast reads everywhere but slows down writes to sync globally.  
* **Time Synchronization:** Uses Hybrid Logical Clocks (HLC) to enforce strict ACID transactions without specialized hardware.

---

## Quick Comparison Matrix

| Feature              | PostgreSQL / MySQL Multi-Region | CockroachDB Native |
|----------------------|---------------------------------|--------------------|
| **Failover / Outages** | Manual or complex failover scripts; risk of data loss. | Automatic via Raft quorum; zero data loss. |
| **Write Routing**      | App must track which node is the current write master. | Any node in any region accepts any query. |
| **Sharding & Scaling** | Manual setup requiring app code logic changes. | Automatic, horizontal, transparent to the app. |
| **Compliance (GDPR)**  | Requires isolated database clusters. | Schema-level row locality rules. |

---

## 3. Azure Solution: Multi-Region Database Architectures

### Azure SQL Database
- **Geo-Replication:** Active geo-replication supports up to 4 readable secondaries in different regions.  
- **Failover Groups:** Automates failover between primary and secondary regions with minimal downtime.  
- **Pattern Mapping:** Matches **Pattern A (Primary-Replica)**.

### Azure Cosmos DB
- **Global Distribution:** Built-in multi-region replication with automatic failover.  
- **Consistency Models:** Strong, bounded staleness, session, eventual.  
- **Pattern Mapping:** Matches **Pattern B (Multi-Primary)** — true multi-master writes.

### Azure PostgreSQL/MySQL Flexible Server
- **Geo-Redundancy:** Supports read replicas across regions.  
- **Pattern Mapping:** Matches **Pattern A (Primary-Replica)**.  
- **Note:** Multi-master requires custom replication frameworks.

### Azure SQL Database Failover Groups (Cloud-Managed)
- **Architecture:** Decouples compute from storage, managed replication.  
- **Pattern Mapping:** Matches **Pattern C (Cloud-Managed)**.

---

## Azure Pattern Mapping Summary

| **Pattern** | **General RDBMS** | **Azure Equivalent** | **Notes** |
|-------------|-------------------|----------------------|-----------|
| **Pattern A: Primary-Replica** | Active-Passive | Azure SQL Database (Geo-Replication, Failover Groups), Azure PostgreSQL/MySQL Read Replicas | Primary for writes, replicas for reads |
| **Pattern B: Multi-Primary** | Active-Active | Azure Cosmos DB (multi-region writes) | True multi-master with consistency models |
| **Pattern C: Cloud-Managed** | Aurora Global style | Azure SQL Database Failover Groups | Managed replication, cloud-native |

---
Diagram: Azure Multi-Region Patterns
```
## ASCII Diagram: Azure Multi-Region Patterns
```
### Pattern A — SQL Database / PostgreSQL / MySQL (Primary-Replica)
```
┌───────────────┐
│ Region 1      │   (Primary: all writes)
└───────▲───────┘
        │ Async Replication
        ▼
┌───────────────┐
│ Region 2      │   (Read Replica: reads only)
└───────────────┘

```
### Pattern B — Cosmos DB Multi-Master
```
┌───────────────┐      ┌───────────────┐      ┌───────────────┐
│ Region 1      │ <--> │ Region 2      │ <--> │ Region 3      │
└───────────────┘      └───────────────┘      └───────────────┘

• All regions accept reads/writes
• Consistency model governs replication


```
### Pattern C — SQL Database Failover Groups (Cloud-Managed)
```
┌───────────────┐
│ Region 1      │   (Primary: writes)
└───────▲───────┘
        │ Managed Replication
        ▼
┌───────────────┐
│ Region 2      │   (Secondary: auto failover)
└───────────────┘

• Decoupled storage
• Automatic failover managed by Azure
```



