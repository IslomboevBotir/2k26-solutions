# Bicycle Rental System
### System Requirements & Architecture Design

---

## Table of Contents

1. [Project Overview](#overview)
2. [Stage 1 — Requirements](#requirements)
   - [Functional Requirements](#functional)
   - [Non-Functional Requirements](#non-functional)
3. [Stage 2 — Architecture](#architecture)
   - [Option A: Monolithic Architecture](#option-a)
   - [Option B: Microservices Architecture](#option-b)
   - [Comparison Table](#comparison)
   - [Final Recommendation](#recommendation)

---

## Project Overview <a name="overview"></a>

A city-wide **bicycle rental platform** that lets users locate, unlock, ride, and return bicycles at docking stations distributed across the city. The platform serves customers via a mobile app and station kiosks, while operators manage the fleet through an admin dashboard.

**Key stakeholders:**
- **Customers** — rent and return bikes via mobile app or kiosk
- **Station operators** — monitor availability and report maintenance issues
- **Administrators** — manage fleet, pricing, analytics, and system health

---

## Stage 1. Requirements <a name="requirements"></a>

### Functional Requirements <a name="functional"></a>

| ID | Name | Description | Priority |
|----|------|-------------|----------|
| FR-1 | **User Registration & Authentication** | Users register with email/password or social OAuth (Google, Apple). Profile stores payment method, rental history, and notification preferences. Password reset and email verification are required. | High |
| FR-2 | **Real-Time Station & Bike Map** | The app displays an interactive map showing all stations, the number of available bikes, free docking slots, and bike condition flags. Data refreshes every 30 seconds or on demand. | High |
| FR-3 | **Rental Initiation & Return** | Users start a rental by scanning a QR code on the bike or entering a bike ID. The system unlocks the bike via IoT command and starts a timer. Return is confirmed when the bike is docked and the lock engages. | High |
| FR-4 | **Pricing & Payment Processing** | Fees are calculated based on rental duration and the selected plan (pay-as-you-go, day pass, monthly subscription). Payments are processed via Stripe. Digital receipts are sent by email and stored in the account. | High |
| FR-5 | **Notifications & Rental History** | Users receive push/email notifications for rental start/end, payment confirmation, unusual trip duration (> 4 hours), and low-battery alerts on e-bikes. Full rental history with map routes is available in-app. | Medium |

---

### Non-Functional Requirements <a name="non-functional"></a>

| ID | Name | Target | Rationale |
|----|------|--------|-----------|
| NFR-1 | **Performance** | API response ≤ 500 ms (P95) at 10,000 concurrent users; map data refresh ≤ 30 s | Users abandon slow apps; real-time availability is the core value proposition |
| NFR-2 | **Scalability** | System must scale horizontally to handle 5× normal load within 5 minutes (e.g., city events) | Uneven, bursty demand is inherent to urban mobility |
| NFR-3 | **Availability** | 99.9% uptime (≤ 8.7 h/year); bike unlock/lock must remain operational during partial outages | Rental interruptions cause user frustration and direct revenue loss |
| NFR-4 | **Security** | TLS 1.3 for all traffic; passwords hashed with bcrypt (cost ≥ 12); PCI DSS Level 1 for payment data; GDPR compliance for EU users | Financial data and location tracking create serious legal and reputational obligations |
| NFR-5 | **Maintainability** | Modular codebase; ≥ 80% automated test coverage; zero-downtime deployments (blue/green or canary); full API versioning | Fleet growth, new bike types, and regulatory changes require frequent safe updates |

---

## Stage 2. Architecture <a name="architecture"></a>

---

### Option A — Monolithic Architecture <a name="option-a"></a>

All application logic is packaged and deployed as a single unit. Internal modules communicate through direct function calls within the same process.

#### System Diagram

```
┌──────────────────────────────────────────────────────────────┐
│                        CLIENT LAYER                          │
│   ┌─────────────────────┐       ┌──────────────────────┐    │
│   │  Mobile App         │       │  Station Kiosk       │    │
│   │  (iOS / Android)    │       │  (embedded browser)  │    │
│   └──────────┬──────────┘       └───────────┬──────────┘    │
└──────────────┼───────────────────────────────┼───────────────┘
               │             HTTPS             │
               ▼                               ▼
┌──────────────────────────────────────────────────────────────┐
│                   LOAD BALANCER  (Nginx)                     │
└──────────────────────────┬───────────────────────────────────┘
                           │
┌──────────────────────────▼───────────────────────────────────┐
│            MONOLITHIC APPLICATION  (Node.js / Django)        │
│                                                              │
│  ┌────────────┐  ┌────────────┐  ┌────────────┐             │
│  │    Auth    │  │   Rental   │  │  Payment   │             │
│  │   Module   │  │   Module   │  │   Module   │             │
│  └────────────┘  └────────────┘  └────────────┘             │
│  ┌────────────┐  ┌────────────┐  ┌────────────┐             │
│  │  Station   │  │   User     │  │  Notify    │             │
│  │   Module   │  │  Profile   │  │   Module   │             │
│  └────────────┘  └────────────┘  └────────────┘             │
│  ┌─────────────────────────────────────────────────────────┐ │
│  │              Shared Business Logic & ORM                │ │
│  └─────────────────────────────────────────────────────────┘ │
└──────────────────────────┬───────────────────────────────────┘
                           │
          ┌────────────────┼────────────────┐
          ▼                ▼                ▼
   ┌─────────────┐  ┌────────────┐  ┌────────────┐
   │ PostgreSQL  │  │   Redis    │  │   Blob     │
   │  (Primary + │  │  (Cache &  │  │  Storage   │
   │   Replica)  │  │  Sessions) │  │  (Receipts)│
   └─────────────┘  └────────────┘  └────────────┘
          │
          ▼
   ┌─────────────────────────┐
   │   IoT Gateway           │
   │   Bike lock/unlock API  │
   │   (HTTP / MQTT)         │
   └─────────────────────────┘
```

#### Technology Stack

| Layer | Technology |
|-------|-----------|
| Backend | Node.js + Express or Python + Django |
| Database | PostgreSQL (primary + read replica) |
| Cache | Redis |
| Auth | JWT + bcrypt |
| Payment | Stripe SDK |
| IoT | MQTT broker (Mosquitto) |
| Deployment | Docker + single VM or small cluster |

#### Advantages

- **Simple local development** — clone one repo, run one process, debug with a single stack trace.
- **Easy deployment** — one Docker image to build and ship; no inter-service networking to configure.
- **Low operational overhead** — no service discovery, distributed tracing, or message broker to maintain.
- **Cost-effective for MVP** — a single mid-range server can comfortably handle thousands of users at low cost.
- **ACID transactions across modules** — multi-step operations (e.g., charge a user and update bike status) stay within a single database transaction, ensuring strong data consistency.

#### Disadvantages

- **Poor independent scalability** — the entire application must be replicated to handle load on one module (e.g., payment spikes require scaling the whole app, including auth and notifications).
- **Single point of failure** — a crash in the Notification module can take down the entire application, making NFR-3 (99.9% uptime) very difficult to achieve.
- **Slow, risky deployments** — any code change requires a full redeployment, increasing the blast radius of every release.
- **Technology lock-in** — all modules must use the same language, runtime, and dependency versions indefinitely.
- **Codebase complexity growth** — as features accumulate, module boundaries erode, leading to tight coupling and increasingly difficult testing.

---

### Option B — Microservices Architecture <a name="option-b"></a>

The system is decomposed into independently deployable services, each owning its own database. Services communicate through an API Gateway (synchronous calls) and a message broker (asynchronous events).

#### System Diagram

```
┌────────────────────────────────────────────────────────────────┐
│                          CLIENT LAYER                          │
│   ┌──────────────────┐              ┌─────────────────────┐   │
│   │  Mobile App      │              │  Station Kiosk      │   │
│   │  (iOS / Android) │              │  (embedded browser) │   │
│   └────────┬─────────┘              └──────────┬──────────┘   │
└────────────┼──────────────────────────────────┼───────────────┘
             │              HTTPS               │
             ▼                                  ▼
┌────────────────────────────────────────────────────────────────┐
│              API GATEWAY  (Kong / AWS API Gateway)             │
│   Auth token validation · Rate limiting · Routing · Logging    │
└────┬──────────────┬───────────────┬────────────────┬───────────┘
     │              │               │                │
     ▼              ▼               ▼                ▼
┌─────────┐  ┌──────────┐  ┌────────────┐  ┌──────────────────┐
│  Auth   │  │  Rental  │  │  Payment   │  │  Notification    │
│ Service │  │  Service │  │  Service   │  │    Service       │
│(Node.js)│  │(Node.js) │  │ (Node.js + │  │  (Node.js +     │
│         │  │          │  │  Stripe)   │  │  Firebase/APNs)  │
└────┬────┘  └────┬─────┘  └─────┬──────┘  └────────┬─────────┘
     │             │              │                   │
     ▼             ▼              ▼                   ▼
┌────────┐  ┌────────────┐  ┌──────────┐  ┌──────────────────┐
│ Users  │  │ Bikes &    │  │ Payments │  │  Notification    │
│   DB   │  │ Rentals DB │  │    DB    │  │    Queue         │
│  (PG)  │  │   (PG)     │  │   (PG)   │  │  (RabbitMQ)      │
└────────┘  └─────┬──────┘  └──────────┘  └──────────────────┘
                  │
                  ▼
          ┌────────────────┐
          │  IoT Gateway   │
          │  (Go service)  │
          │  MQTT / HTTP   │
          └────────────────┘

  Async event flow via Message Broker (RabbitMQ / Kafka):
  ┌───────────────────────────────────────────────────┐
  │  Topics: rental.started · rental.ended            │
  │          payment.confirmed · bike.unlocked/locked  │
  └───────────────────────────────────────────────────┘

  Shared infrastructure:
  ┌───────────────────┐   ┌───────────────────────────┐
  │  Redis            │   │  Kubernetes (EKS / GKE)   │
  │  (shared cache,   │   │  Service mesh (Istio)      │
  │  availability)    │   │  Distributed tracing       │
  └───────────────────┘   └───────────────────────────┘
```

#### Technology Stack

| Service | Technology | Database |
|---------|-----------|----------|
| API Gateway | Kong or AWS API Gateway | — |
| Auth Service | Node.js + Passport.js | PostgreSQL |
| Rental Service | Node.js | PostgreSQL |
| Payment Service | Node.js + Stripe SDK | PostgreSQL (PCI-isolated) |
| Notification Service | Node.js + Firebase/APNs | RabbitMQ queue |
| IoT Gateway | Go (low-latency, high concurrency) | Redis (lock state) |
| Message Broker | RabbitMQ or Kafka | — |
| Orchestration | Kubernetes (EKS / GKE) | — |
| Observability | Datadog or Jaeger + Prometheus + Grafana | — |

#### Advantages

- **Independent scalability** — during rush hour, only the Rental Service and IoT Gateway scale up; Auth and Notifications remain at baseline resource usage, reducing infrastructure costs.
- **Fault isolation** — a crash in the Notification Service does not affect the ability to unlock a bike; the critical rental path remains operational.
- **Technology flexibility** — the IoT Gateway can be written in Go for maximum throughput and low latency, independent of what other services use.
- **Faster, safer releases** — each service has its own CI/CD pipeline; a broken notification feature cannot block an urgent payment fix.
- **PCI DSS isolation** — the Payment Service can be deployed in a segregated network namespace with its own access controls and compliance scope, without constraining other services.
- **Organizational scalability** — multiple teams can own separate services and release independently without blocking each other.

#### Disadvantages

- **High operational complexity** — requires Kubernetes, service discovery, distributed tracing, centralized logging, and a message broker, all of which need to be configured and maintained.
- **Distributed data consistency** — ensuring consistency across services (e.g., charge the user AND mark the bike as rented) requires the Saga pattern rather than a simple ACID transaction.
- **Network overhead** — synchronous inter-service calls add 5–20 ms per hop; errors can cascade across service boundaries if circuit breakers are not properly configured.
- **Higher initial cost** — multiple databases, a message broker, a service mesh, and a Kubernetes cluster require substantially more infrastructure investment upfront.
- **Debugging complexity** — a single user request spans multiple services; reproducing and tracing bugs requires distributed tracing tooling and broader team knowledge.

---

### Architecture Comparison <a name="comparison"></a>

| Criteria | Option A — Monolith | Option B — Microservices |
|----------|---------------------|--------------------------|
| Development speed (MVP) | ✅ Fast — one codebase, one process | ❌ Slower — service scaffolding overhead |
| Scalability (NFR-2) | ⚠️ Scale all-or-nothing | ✅ Scale each service independently |
| Fault isolation (NFR-3) | ❌ Single failure can cause full outage | ✅ Failures are contained per service |
| Deployment risk | ❌ Full redeploy for every change | ✅ Isolated, incremental deployments |
| Operational complexity | ✅ Low | ❌ High (K8s, broker, tracing, mesh) |
| Data consistency | ✅ Single DB, ACID transactions | ❌ Eventual consistency / Saga pattern |
| Technology flexibility | ❌ Single stack for all modules | ✅ Best tool per service |
| Initial infrastructure cost | ✅ Low | ❌ Higher |
| PCI DSS isolation | ⚠️ Requires extra configuration | ✅ Network-level isolation per service |
| Long-term maintainability | ⚠️ Degrades as codebase grows | ✅ Clean service boundaries enforced |

---

### Final Recommendation <a name="recommendation"></a>

#### Recommended: Option B — Microservices Architecture

For a **production bicycle rental system operating at city scale**, microservices is the correct long-term architectural choice for three primary reasons:

**1. Scalability is a hard requirement, not a nice-to-have.**
NFR-2 demands 5× traffic spikes within minutes. A monolith scales all or nothing — handling a rush-hour surge in bike unlocking would force scaling of the payment module, auth module, and notifications simultaneously, wasting resources and money. With microservices, only the Rental Service and IoT Gateway autoscale.

**2. 99.9% uptime cannot be reliably achieved with a monolith.**
NFR-3 allows less than 9 hours of downtime per year. A memory leak, uncaught exception, or bad deployment in any module of a monolith can bring down the entire system. With microservices, the Notification Service going down has zero effect on a user trying to unlock a bike.

**3. IoT and payment are natural service boundaries.**
Bike lock/unlock operates over MQTT with sub-second latency requirements and high concurrent fan-out — properties where Go dramatically outperforms Node.js or Django. Payment processing requires PCI DSS network isolation that is simple to achieve as a separate service and complex to achieve inside a shared monolith.

