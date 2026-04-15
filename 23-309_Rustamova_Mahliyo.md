# Baby Vaccination Reminder System

---

## Stage 1. Requirements

### Functional Requirements

| # | Requirement | Description |
|---|-------------|-------------|
| 1 | **User Registration & Login** | Parents can register and log in using email/password or phone number. |
| 2 | **Child Profile Creation** | Parents can create a child profile with name, date of birth, gender, and medical notes. |
| 3 | **Automatic Vaccination Schedule** | The system automatically generates a vaccination schedule based on the child's age. |
| 4 | **Reminder Notifications** | The system sends reminders via SMS, email, or push notification before each vaccination date. |
| 5 | **Vaccination History** | Parents can view and update the vaccination history for each child. |

---

### Non-Functional Requirements

| # | Category | Requirement |
|---|----------|-------------|
| 1 | **Security** | All child medical data must be stored using encryption (AES-256). |
| 2 | **Reliability** | Reminder notifications must be delivered on time with 99% reliability. |
| 3 | **Performance** | All pages must load within 2 seconds under normal network conditions. |
| 4 | **Scalability** | The system must handle profiles and data for 100,000+ users. |
| 5 | **Usability** | The interface must be simple enough for non-technical parents to use without guidance. |

---

## Stage 2. Architecture

### Option 1 — Monolithic Architecture

#### System Diagram

```
+----------------------------------------------------------+
|          Baby Vaccination Reminder System                |
|                   (Single App)                           |
|                                                          |
|  +-------------+  +-------------+  +-----------------+  |
|  |    Auth     |  |    Child    |  |   Vaccination   |  |
|  |   Module    |  |   Profile   |  |    Schedule     |  |
|  |             |  |   Module    |  |    Module       |  |
|  +-------------+  +-------------+  +-----------------+  |
|                                                          |
|  +-------------+  +-------------+  +-----------------+  |
|  | Reminder &  |  |  Vaccination|  |     Admin       |  |
|  | Notification|  |   History   |  |     Module      |  |
|  |   Module    |  |   Module    |  |                 |  |
|  +-------------+  +-------------+  +-----------------+  |
|                                                          |
+----------------------------+-----------------------------+
                             |
                   +---------v---------+
                   |    PostgreSQL     |
                   |     Database     |
                   +------------------+
```

#### Advantages
- Simple to develop and deploy — all modules are in one place
- Easy to test and debug during early development
- Lower infrastructure and operational cost at the start
- Faster to build for a small development team

#### Disadvantages
- Difficult to scale only one module independently (e.g., Notification service)
- A bug in one module can crash the entire system
- As the codebase grows, it becomes harder to maintain
- Any small update requires redeploying the entire application

---

### Option 2 — Microservices Architecture

#### System Diagram

```
                    +----------------------+
                    |     API Gateway      |
                    |  (Routes requests)   |
                    +-----------+----------+
                                |
       +------------------------+------------------------+
       |           |            |            |           |
+------v----+ +----v-----+ +----v-----+ +----v-----+ +--v-------+
|   Auth    | |  Child   | |  Vaccine | |Reminder  | |  Admin   |
|  Service  | | Profile  | | Schedule | | Service  | | Service  |
|           | | Service  | | Service  | |          | |          |
+-----------+ +----------+ +----------+ +----------+ +----------+
      |             |            |            |
 +----v----+  +-----v---+  +-----v---+  +-----v---------+
 | Users   |  | Child   |  |Schedule |  | Notification  |
 |   DB    |  |  DB     |  |   DB    |  | (SMS/Email/   |
 +---------+  +---------+  +---------+  |  Push)        |
                                        +---------------+

                    +----------------------+
                    |   Vaccination        |
                    |   History Service    |
                    |                      |
                    +----------+-----------+
                               |
                    +----------v-----------+
                    |    History DB        |
                    +----------------------+
```

#### Advantages
- Each service can be scaled independently (e.g., scale only the Notification service during peak hours)
- Failure in one service does not crash the entire platform
- Different teams can work on different services at the same time
- Easier to update, replace, or improve individual services

#### Disadvantages
- More complex to set up, configure, and maintain
- Requires extra infrastructure (API Gateway, message broker, Docker, etc.)
- End-to-end testing is more difficult
- Higher operational cost, especially for a small team at early stage

---

### Which Option is Better and Why?

**For this project — Option 1 (Monolithic) is recommended at the start.**

#### Reasoning:

| Factor | Monolithic | Microservices |
|--------|-----------|---------------|
| Team size | Small team — easier to manage | Requires a larger, specialized team |
| Development speed | Faster at early stage | Slower due to complex setup |
| Infrastructure cost | Low | High |
| Scalability | Limited | High |
| Complexity | Low | High |
| Medical data handling | Centralized, easier to secure | Distributed, harder to secure |

A Baby Vaccination Reminder System is a medium-scale application.
At the beginning, a **Monolithic Architecture** allows faster development, easier data security management, and lower cost — which is critical when handling sensitive medical data of children.

When the user base scales to hundreds of thousands and the Notification or Schedule services require more performance, the system can be **gradually migrated to Microservices**.

> **Conclusion:** Start with Monolithic → migrate to Microservices when the user base and load demand it.
