# Subscription Management Service

## Stage 1. Requirements

### Project Overview

A **Subscription Management Service** is a platform that allows businesses to manage recurring billing, customer subscriptions, plan changes, payment processing, and subscription lifecycle events (trials, renewals, cancellations, upgrades/downgrades).

---

### Functional Requirements

| # | Requirement | Description |
|---|-------------|-------------|
| FR-1 | **Subscription Lifecycle Management** | Users can create, pause, resume, upgrade, downgrade, and cancel subscriptions. The system must track subscription status transitions (active → paused → cancelled, etc.) and trigger appropriate actions at each transition. |
| FR-2 | **Recurring Billing & Payment Processing** | The system must automatically charge customers on a recurring schedule (daily, weekly, monthly, annual). It must support multiple payment methods (credit/debit card, PayPal, bank transfer) and handle payment failures with retry logic. |
| FR-3 | **Plan & Pricing Management** | Administrators must be able to create and configure subscription plans with custom pricing, billing cycles, feature limits, and trial periods. Plans must support discounts, coupons, and promotional pricing. |
| FR-4 | **Invoice & Receipt Generation** | The system must automatically generate itemized invoices for every billing event and send PDF receipts to customers via email. Users must be able to access their full billing history through a self-service portal. |
| FR-5 | **Notifications & Alerts** | The system must send automated email/SMS notifications for key events: upcoming renewals (3 days before), successful payments, failed payments, trial expiry warnings, and cancellation confirmations. |

---

### Non-Functional Requirements

| # | Requirement | Description |
|---|-------------|-------------|
| NFR-1 | **Availability** | The service must maintain **99.9% uptime** (≤ 8.7 hours downtime/year). Billing and payment endpoints must have redundancy to prevent revenue loss during outages. |
| NFR-2 | **Performance** | API responses for subscription queries must return in **< 200 ms** (p95). Batch billing jobs must be capable of processing **10,000+ subscriptions per hour** without degrading real-time API performance. |
| NFR-3 | **Security & Compliance** | All payment data must comply with **PCI DSS Level 1**. Sensitive cardholder data must never be stored directly — only tokenized references from a certified payment gateway. All data at rest and in transit must be encrypted (AES-256 / TLS 1.3). |
| NFR-4 | **Scalability** | The system must scale horizontally to support growth from 1,000 to 1,000,000+ active subscriptions without architectural changes. Auto-scaling must be triggered when CPU or queue depth exceeds defined thresholds. |
| NFR-5 | **Maintainability & Observability** | The system must expose structured logs, distributed traces, and metrics (error rate, billing success rate, latency) to a centralized monitoring platform. Code must maintain **> 80% test coverage** and support zero-downtime deployments. |

---

## Stage 2. Architecture

---

### Option A — Monolithic Architecture (Layered Monolith)

#### System Diagram

```
┌─────────────────────────────────────────────────────────────┐
│                         CLIENT LAYER                        │
│          Web App (React)        Mobile App (React Native)   │
└────────────────────────┬────────────────────────────────────┘
                         │ HTTPS
┌────────────────────────▼────────────────────────────────────┐
│                  MONOLITHIC APPLICATION                      │
│                                                             │
│   ┌──────────────┐  ┌──────────────┐  ┌──────────────────┐ │
│   │  Subscription│  │   Billing &  │  │  Notification    │ │
│   │   Module     │  │  Invoicing   │  │    Module        │ │
│   │              │  │   Module     │  │                  │ │
│   └──────┬───────┘  └──────┬───────┘  └────────┬─────────┘ │
│          │                 │                   │           │
│   ┌──────▼─────────────────▼───────────────────▼─────────┐ │
│   │               Shared Business Logic Layer             │ │
│   └──────────────────────────┬────────────────────────────┘ │
│                              │                             │
│   ┌───────────────────────────▼──────────────────────────┐ │
│   │                  Data Access Layer (ORM)             │ │
│   └───────────────────────────┬──────────────────────────┘ │
└───────────────────────────────┼─────────────────────────────┘
                                │
          ┌─────────────────────┼─────────────────────┐
          │                     │                     │
┌─────────▼──────┐   ┌──────────▼──────┐   ┌─────────▼──────┐
│  PostgreSQL DB  │   │   Redis Cache   │   │ Payment Gateway │
│  (Primary +    │   │  (Sessions,     │   │  (Stripe API)   │
│   Replica)     │   │   Rate Limits)  │   └────────────────┘
└────────────────┘   └─────────────────┘
```

#### Advantages

- **Simplicity**: Easy to develop, test, and deploy as a single unit. Ideal for small teams at early stages.
- **Low operational overhead**: Only one service to monitor, deploy, and maintain. No network calls between internal components.
- **Easier debugging**: Stack traces and logs are unified. Transactions span all modules without distributed coordination.
- **Faster initial development**: No need to design inter-service contracts or manage service discovery.

#### Disadvantages

- **Scalability bottleneck**: Cannot scale individual modules independently. A spike in billing jobs slows the entire application.
- **Deployment risk**: Every change requires redeploying the entire application, increasing the blast radius of bugs.
- **Technology lock-in**: All modules must use the same language, framework, and runtime version.
- **Growing complexity**: As features grow, the codebase becomes harder to understand. Modules become tightly coupled over time.
- **Single point of failure**: A fatal error in one module (e.g., invoice generation) can crash the entire service.

---

### Option B — Microservices Architecture

#### System Diagram

```
┌─────────────────────────────────────────────────────────────┐
│                         CLIENT LAYER                        │
│          Web App (React)        Mobile App (React Native)   │
└────────────────────────┬────────────────────────────────────┘
                         │ HTTPS
┌────────────────────────▼────────────────────────────────────┐
│                     API GATEWAY                             │
│         (Auth, Rate Limiting, Routing, Load Balancing)      │
└──┬──────────┬──────────────┬──────────────────┬─────────────┘
   │          │              │                  │
   ▼          ▼              ▼                  ▼
┌──────┐  ┌────────┐  ┌───────────┐  ┌──────────────────┐
│ Sub  │  │Billing │  │ Invoice   │  │  Notification    │
│Service│  │Service │  │  Service  │  │    Service       │
│      │  │        │  │           │  │                  │
└──┬───┘  └───┬────┘  └─────┬─────┘  └────────┬─────────┘
   │          │              │                  │
   └──────────┴──────────────┴──────────────────┘
                             │
              ┌──────────────▼──────────────┐
              │        MESSAGE BROKER        │
              │    (RabbitMQ / Kafka)        │
              │  Events: subscription.created│
              │  billing.failed, invoice.sent│
              └──────────────────────────────┘
   │          │              │                  │
┌──▼──────┐ ┌─▼────────┐ ┌───▼──────┐ ┌────────▼───────┐
│Postgres │ │Postgres  │ │Postgres  │ │   Email/SMS    │
│(Subs DB)│ │(Bills DB)│ │(Invoice) │ │ Provider API   │
└─────────┘ └──────────┘ └──────────┘ └────────────────┘
              ┌────────────────────┐
              │   Shared Services  │
              │  Redis (Cache)     │
              │  Vault (Secrets)   │
              │  Prometheus/Grafana│
              └────────────────────┘
```

#### Advantages

- **Independent scalability**: The Billing Service can be scaled up during billing cycles without touching other services.
- **Fault isolation**: A crash in the Notification Service does not affect billing or subscription management.
- **Technology flexibility**: Each service can use the best-fit stack (e.g., Node.js for the API gateway, Python for billing logic).
- **Independent deployments**: Teams can release services independently with no downtime across the system.
- **Better long-term maintainability**: Smaller codebases per service are easier to understand and onboard new developers.

#### Disadvantages

- **Higher operational complexity**: Requires container orchestration (Kubernetes), service discovery, distributed tracing, and centralized logging.
- **Distributed systems challenges**: Network failures, eventual consistency, and distributed transactions (e.g., saga pattern) add significant complexity.
- **More infrastructure cost**: Running multiple databases, brokers, and services is more expensive than a single monolith at small scale.
- **Slower initial development**: Designing service contracts, event schemas, and inter-service communication takes more upfront effort.

---

### Recommended Option: **Option B — Microservices Architecture**

**For a Subscription Management Service, the microservices architecture is the better long-term choice**, for the following reasons:

1. **Billing is time-sensitive and resource-intensive.** Monthly billing runs must process thousands of charges simultaneously without slowing down the user-facing subscription portal. Independent scaling of the Billing Service is essential.

2. **Compliance requirements demand isolation.** PCI DSS compliance is significantly easier to scope and audit when payment-handling logic is isolated in a dedicated Billing Service with its own network perimeter, rather than embedded in a monolith.

3. **Different availability requirements per module.** The API and subscription management must be always-on, while invoicing and notification services can tolerate brief delays. Microservices allow different SLAs per service.

4. **Team scalability.** As the product grows, separate teams can own separate services without merge conflicts or deployment coordination.

> **Note:** For early-stage development (MVP with < 5,000 subscribers), starting with a **well-structured modular monolith** and migrating to microservices later is a pragmatic approach that reduces initial complexity while preserving the ability to extract services when needed.
