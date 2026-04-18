# Remote Team Collaboration Board
## Requirements

### Project overview
A web-based real-time collaboration board that allows remote teams to manage tasks, communicate, and track project progress together — similar in spirit to a hybrid of Trello and Notion.

---

### Functional requirements

**FR-1 — User authentication and access control**
The system must support user registration, login (including OAuth via Google / GitHub), and role-based permissions. Boards can be private (invite-only), team-shared, or public. Board owners can assign roles: Owner, Editor, Viewer.

**FR-2 — Board and card management**
Users must be able to create, update, archive, and delete boards. Each board contains columns (e.g. To Do, In Progress, Done) and cards. Cards support a title, rich-text description, due date, labels, attachments, and assignees.

**FR-3 — Real-time collaboration**
All changes to a board — moving cards, editing titles, adding comments — must be instantly visible to all active collaborators without a page refresh. The system must use WebSockets (Laravel Reverb or Pusher) to broadcast events.

**FR-4 — Notifications and activity feed**
Users must receive in-app and email notifications for events they are involved in: being assigned to a card, receiving a comment mention (@username), approaching due dates. Each board must have an activity feed showing a timestamped log of all changes.

**FR-5 — File attachments and media**
Users must be able to upload files and images to cards. Supported formats include images (JPG, PNG, GIF), documents (PDF, DOCX), and archives (ZIP). Files are stored in object storage (S3-compatible). Cards display image previews inline.

---

### Non-functional requirements

**NFR-1 — Performance**
API responses for standard board operations (loading a board, moving a card) must complete in under 300 ms for 95% of requests under normal load. The system must use Redis for caching frequently accessed data such as board state and user sessions.

**NFR-2 — Scalability**
The system must support at least 500 concurrent WebSocket connections and 100 simultaneous active boards per server instance. The architecture must allow horizontal scaling of the application tier without redesigning core components.

**NFR-3 — Security**
All data must be transmitted over HTTPS. Authentication tokens must use short-lived JWTs. The API must be protected against common vulnerabilities: SQL injection (via Eloquent ORM), XSS (output escaping), CSRF (Laravel middleware), and rate limiting on auth endpoints.

**NFR-4 — Availability and reliability**
The system must target 99.5% uptime. Background jobs (notifications, file processing) must use a persistent queue (Redis-backed Laravel Queue) with automatic retry logic. Failed jobs must be logged and made visible in an admin panel.

**NFR-5 — Maintainability**
The codebase must follow PSR-12 coding standards and Laravel best practices (service classes, form requests, resource controllers). All business logic must be covered by feature and unit tests with a minimum 80% code coverage, enforced in CI/CD.

---

## Architecture

### 1) Monolithic application

#### System diagram

```
┌─────────────────────────────────────────────────────────┐
│               Single Laravel application                │
│                                                         │
│  ┌─────────────────────────────────────────────────┐    │
│  │           Routes + Controllers         		  │   │
│  └─────────────────┬───────────────────────────────┘    │
│                    │                                    │
│  ┌─────────────────▼───────────────────────────────┐    │
│  │  Service Layer │ Queue Jobs │ Event Broadcasts   │   │
│  └─────────┬──────┴─────┬──────┴──────────┬─────────┘   │
│            │             │                 │            │
│       ┌────▼──┐    ┌────▼───┐      ┌─────▼──────┐       │
│       │ MySQL │    │ Redis  │      │ S3 Storage │       │
│       └───────┘    └────────┘      └────────────┘       │
└─────────────────────────────────────────────────────────┘
```

All application logic (HTTP controllers, WebSocket broadcasting, queue workers, file handling) lives inside a single Laravel project deployed on one or more identical servers behind a load balancer.

**Key components:**
- **Laravel Reverb** — WebSocket server integrated into the Laravel process for real-time board updates
- **Laravel Queue** — Background job processing for email notifications and file uploads, backed by Redis
- **Eloquent ORM** — All database access via models and repositories
- **Laravel Sanctum** — API token authentication
- **MySQL** — Primary relational database for all entities (users, boards, cards, comments)
- **Redis** — Session cache, queue driver, and pub/sub channel for broadcasting

#### Advantages

- **Simpler deployment** — a single `composer install` and one server configuration covers the entire application.
- **Easier local development** — one repository, one `php artisan serve`, one `.env` file.
- **Straightforward debugging** — stack traces, logs, and feature tests all live in one place.
- **Lower infrastructure cost** — no inter-service networking, no service discovery, no API gateway overhead.
- **Laravel ecosystem fits perfectly** — Reverb, Horizon, Sanctum, and Queue are all first-party packages designed to work together in a monolith.
- **Faster initial delivery** — the team avoids the operational overhead of containers and service orchestration at the start.

#### Disadvantages

- **Scaling bottlenecks** — if the WebSocket layer becomes the bottleneck, you cannot scale it independently without scaling the entire app.
- **Deployment risk** — every release deploys everything; a bug in notifications can take down boards.
- **Growing complexity** — as features accumulate, the codebase can become harder to navigate without strict discipline.
- **Technology lock-in** — the entire stack must run on PHP/Laravel; introducing a specialised service (e.g. a Go-based WebSocket server) requires significant refactoring.

---

### 2) API gateway + microservices

#### System diagram

```
	  Client
         │
         ▼
┌─────────────────────────┐
│  Laravel API Gateway    │ 
└──────────┬──────────────┘
           │
    ┌──────┴──────────────────────────────────┐
    │              │              │            │
┌───▼────┐  ┌─────▼────┐  ┌────▼─────┐  ┌──▼──────┐
│ Auth   │  │  Board   │  │ Notif.   │  │  File   │
│ svc    │  │  svc     │  │  svc     │  │  svc    │
└───┬────┘  └─────┬────┘  └────┬─────┘  └──┬──────┘
    │             │             │            │
    └─────────────┴──────┬──────┴────────────┘
                         │
              ┌──────────▼──────────┐
              │   Message Bus       │
              │   (Redis)           │
              └──────────┬──────────┘
                         │
         ┌───────────────┼────────────────┐
    ┌────▼─────┐  ┌─────▼────┐  ┌────────▼──────┐
    │ Realtime │  │ MySQL    │  │ Redis (cache) │
    │ svc (WS) │  │ per-svc  │  │               │
    └──────────┘  └──────────┘  └───────────────┘
```

The Laravel API gateway handles all inbound HTTP requests, validates tokens, and routes to independent services. Each service owns its own data store and communicates asynchronously via a message bus.

**Key components:**
- **Laravel API Gateway** — thin Laravel app that handles auth middleware, request validation, and proxies to internal services via HTTP or a message bus
- **Board service** — dedicated Laravel app managing boards, columns, cards, and comments with its own MySQL schema
- **Auth service** — issues and validates JWTs; can be a shared Laravel Passport server
- **Notification service** — subscribes to board events and dispatches emails and push notifications
- **Realtime service** — standalone WebSocket server that clients subscribe to
- **File service** — handles upload, validation, thumbnail generation, and S3 storage
- **Redis / SQS** — the message bus that decouples services; board events are published and services consume independently

#### Advantages

- **Independent scaling** — the WebSocket service and board service can be scaled to different replica counts based on real demand.
- **Fault isolation** — a crash in the notification service does not affect board editing.
- **Technology flexibility** — the realtime service could be replaced with a Go or Node.js implementation without touching the Laravel board service.
- **Smaller, focused codebases** — each service is easier to understand, test, and deploy on its own.
- **Clear ownership** — different team members or squads can own different services.

#### Disadvantages

- **Higher operational complexity** — requires container orchestration (Docker Compose / Kubernetes), service discovery, and centralized logging.
- **Harder to develop locally** — developers need all services running to test end-to-end flows.
- **Distributed tracing needed** — debugging a request that spans three services requires correlation IDs and a tool like Jaeger or Datadog.
- **Data consistency challenges** — operations that span multiple services (e.g. creating a card and sending a notification) require eventual consistency or a saga pattern instead of a simple database transaction.
- **Higher initial cost** — more infrastructure, more DevOps effort, more CI/CD pipelines to maintain.

---

### Recommendation: Option A — Monolithic application

**Option A is the better choice for this project at this stage.**

The core reasons are:

1. **Stack alignment** — the project is built on Laravel, and Laravel's first-party tools (Reverb for WebSockets, Horizon for queue monitoring, Sanctum for auth) are all designed to work together inside a single well-structured monolith. Splitting them into microservices would add complexity without meaningful benefit at this scale.

2. **Team and project size** — for a collaboration board serving hundreds to low thousands of users, the operational overhead of managing multiple services outweighs the scalability benefit. A well-structured monolith can comfortably handle this load.

3. **Development velocity** — a single repository with shared models, migrations, and tests allows faster iteration. Bug fixes and feature additions do not require coordinating deployments across multiple services.

4. **Migration path preserved** — a monolith built with clear service classes and bounded domains (e.g. `App\Boards`, `App\Notifications`) can be extracted into microservices later if a specific component genuinely becomes a bottleneck. This is the "modular monolith" approach and is a well-established path.

Option B becomes appropriate when there is a proven need to scale a specific component independently — for example, if WebSocket connections reach tens of thousands — or when multiple autonomous teams need to deploy independently. At this point, extracting just the WebSocket layer (Reverb → dedicated server) would be the first sensible step, not a full microservices split.
