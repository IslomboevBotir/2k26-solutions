This is a comprehensive technical specification and architectural blueprint for an **Online Flower Delivery Service**. The structure follows professional software engineering standards, moving from high-level requirements to low-level system design.

---

# Project Specification: BloomDash (Online Flower Delivery)

## 1. Project Overview
**BloomDash** is a specialized e-commerce platform designed to bridge the gap between local florists and customers. The system manages the entire lifecycle of a flower order: from inventory management and customer selection to real-time delivery tracking via GPS.

---

## 2. Requirements Specification

### 2.1 Functional Requirements (FR)
| ID | Module | Feature Description |
| :--- | :--- | :--- |
| **FR-1** | **User Management** | Multi-role authentication (Customer, Florist, Courier). Support for OTP (One-Time Password) login and Social Auth (Google). |
| **FR-2** | **Product Catalog** | Dynamic inventory with seasonal categories, price filters, and stock management. Support for "Add-ons" (chocolates, cards). |
| **FR-3** | **Order Engine** | Shopping cart, scheduled delivery times, personalized message notes, and delivery address geo-coding. |
| **FR-4** | **Payment Gateway** | Secure integration with local (Click, Payme) and international (Stripe) providers. Refund and transaction logging. |
| **FR-5** | **Live Logistics** | Real-time tracking of couriers on a map for customers. Order assignment logic for the nearest available courier. |

### 2.2 Non-Functional Requirements (NFR)
*   **NFR-1 (Performance):** Catalog page load time under **1.5 seconds** using Redis caching.
*   **NFR-2 (Scalability):** Capability to handle a **10x traffic spike** during holidays (e.g., Valentine’s Day) via horizontal scaling.
*   **NFR-3 (Reliability):** Database ACID compliance for payment consistency. 99.9% uptime target.
*   **NFR-4 (Security):** All API traffic via TLS/SSL. Encrypted storage for sensitive customer phone numbers.

---

## 3. System Architecture (Monolithic Model)

To ensure rapid development and data consistency, we utilize a **Modular Monolith** architecture based on the Laravel ecosystem.

### 3.1 Logical Architecture Diagram

```text
       [ Clients: Web Browser / Mobile Web ]
                    │
                    ▼
       ┌─────────────────────────────────────┐
       │       Laravel API Gateway / UI      │
       ├─────────────────────────────────────┤
       │  [ Auth ]  [ Orders ]  [ Catalog ]  │
       │  [ Payments ]   [ Notifications ]   │
       └──────────────────┬──────────────────┘
                    │
      ┌─────────────┴─────────────┐
      ▼                           ▼
┌───────────┐               ┌───────────┐
│   MySQL   │               │   Redis   │
│ (Primary  │               │ (Queues & │
│  Storage) │               │  Caching) │
└───────────┘               └───────────┘
      │                           │
      ▼                           ▼
┌───────────┐               ┌───────────┐
│ AWS S3    │               │ Laravel   │
│ (Product  │               │ Reverb    │
│  Images)  │               │ (Websocket)│
└───────────┘               └───────────┘
```

---

## 4. Data Modeling (ERD)

The database is designed to handle high-concurrency transactions.

### Core Entities:
*   **Users:** `id, phone, email, password, role_id`
*   **Products:** `id, name, description, price, stock, florist_id`
*   **Orders:** `id, customer_id, courier_id, total_price, status, delivery_lat, delivery_long, scheduled_at`
*   **Order_Items:** `id, order_id, product_id, quantity, unit_price`
*   **Courier_Locations:** `id, courier_id, current_lat, current_long, last_updated`

---

## 5. Order Execution Flow

The sequence of events follows a strict state-machine logic to prevent data loss:

1.  **Placement:** Customer pays; Order status = `Pending`.
2.  **Preparation:** Florist receives notification and accepts; Status = `Preparing`.
3.  **Dispatch:** Order is ready; System pings the nearest Courier.
4.  **In-Transit:** Courier picks up; Status = `On the Way`. Real-time WebSocket stream starts.
5.  **Completion:** Courier confirms delivery with a photo; Status = `Delivered`.

---

## 6. Technology Stack

*   **Framework:** **Laravel 11** (PHP 8.3) - chosen for its robust built-in features for queues and auth.
*   **Frontend:** **Vue.js 3** with **Inertia.js** - provides a Single Page Application (SPA) feel without the complexity of a separate API.
*   **Database:** **PostgreSQL** or **MySQL** (Relational data).
*   **Real-time:** **Laravel Reverb** (WebSockets) for the live map tracking.
*   **Queue Driver:** **Redis** for managing high volumes of SMS and Email notifications.

---

## 7. Implementation Roadmap

### Phase 1: Foundation (Weeks 1-3)
*   Database schema design.
*   Auth system (Customer/Florist/Courier) and basic Catalog management.

### Phase 2: Transactional Core (Weeks 4-6)
*   Shopping cart logic.
*   Payment gateway integration (Click/Stripe).
*   Order state management.

### Phase 3: Logistics & Real-time (Weeks 7-9)
*   Courier dashboard.
*   Google Maps API / Mapbox integration for delivery tracking.
*   WebSocket implementation for "Live Map."

### Phase 4: Launch & Scale (Weeks 10+)
*   Load testing for holiday traffic simulation.
*   Deployment to AWS or DigitalOcean with Auto-scaling.
