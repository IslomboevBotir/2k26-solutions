# Blood Donor Matching Service
## Requirements

### Project overview
A cross-platform mobile and web application built with Flutter that connects blood donors with patients, hospitals, and blood banks in real time. The service allows donors to register their blood type and availability, enables hospitals and individuals to submit urgent blood requests, and automatically matches and notifies compatible nearby donors — similar in spirit to a hybrid of a medical dispatch system and a community volunteer network.

---

### Functional requirements

**FR-1 — User registration and profile management**
The system must support registration and login for two user roles: **Donors** and **Requesters** (patients, hospitals, or blood banks). OAuth via Google is supported for quick sign-up using Firebase Authentication. Donor profiles must store blood type, location (city / coordinates), contact information, donation history, and availability status (available / temporarily unavailable). Requesters must be able to verify their identity as a medical institution via document upload.

**FR-2 — Blood request submission and management**
Requesters must be able to submit a blood request specifying: required blood type, quantity (units), urgency level (routine / urgent / critical), location, and an optional expiry deadline. Requests must support statuses: open, partially fulfilled, fulfilled, and expired. Requesters can update or cancel open requests. The system must automatically expire requests past their deadline using scheduled Cloud Functions.

**FR-3 — Donor matching and notification**
When a blood request is submitted, the system must automatically identify compatible donors (following standard ABO/Rh compatibility rules) within a configurable radius using Firestore geoqueries. Matched donors must receive an immediate push notification via Firebase Cloud Messaging (FCM) containing the request details, urgency level, and a one-tap accept / decline response. The system must re-notify the next batch of donors if the initial matches do not respond within a configurable timeout, triggered by a scheduled Cloud Function.

**FR-4 — Donation history and eligibility tracking**
The system must track each donor's full donation history (date, location, blood bank, units donated). It must enforce a minimum waiting period between donations (56 days for whole blood by default, configurable per donation type). Donors within the waiting period must be automatically marked as ineligible and excluded from matching until the period expires, recalculated via a nightly Cloud Function trigger.

**FR-5 — Search, map view, and analytics dashboard**
Donors must be searchable by blood type, city, and availability. A map view (Google Maps Flutter plugin) must show nearby available donors and active requests. Requesters must have a dashboard showing their request history and fulfillment statistics. Administrators must have access to a system-wide analytics panel showing donation volumes, response rates by region, average match time, and donor activity trends, powered by aggregated Firestore data.

---

### Non-functional requirements

**NFR-1 — Performance**
The Flutter UI must render at a consistent 60 fps on mid-range Android and iOS devices. Firestore real-time listeners must reflect blood request status changes within 1 second under normal network conditions. The donor-matching Cloud Function must complete and dispatch FCM notifications within 2 seconds of a new request being written, even for regions with up to 50 000 registered donors. Frequently read data (compatibility tables, active request lists) must be cached locally using Hive or flutter_cache_manager.

**NFR-2 — Scalability**
The system must support at least 10 000 registered donors and 1 000 simultaneous active blood requests. Firebase Cloud Firestore and Cloud Functions scale automatically without manual infrastructure provisioning. Notification dispatch must be handled asynchronously by Cloud Functions so that a spike in critical requests does not block the client. The architecture must support horizontal growth without any server-side re-engineering.

**NFR-3 — Security and privacy**
All data must be transmitted over HTTPS. Authentication must use Firebase Auth tokens (short-lived JWTs managed automatically by the Firebase SDK). Firestore Security Rules must enforce role-based access: donors can only read their own profiles and active requests; requesters can only modify their own requests. Donor personal data (phone number, exact coordinates) must be stored in a restricted Firestore sub-collection and revealed only upon a confirmed match. The API must be protected against abuse via Firebase App Check and Cloud Function rate-limiting.

**NFR-4 — Availability and reliability**
The system must target 99.5% uptime, leveraging Firebase's managed infrastructure SLA. Notification Cloud Functions must implement retry logic (up to 3 attempts with exponential back-off) for failed FCM deliveries. Failed function invocations must be logged in Google Cloud Logging and monitored via Cloud Monitoring alerts. Scheduled eligibility recalculation must run reliably via Cloud Scheduler.

**NFR-5 — Maintainability and testability**
The Flutter codebase must follow the feature-first folder structure with clean separation of layers (presentation, domain, data) using a state management solution such as Riverpod or BLoC. All matching and eligibility business logic must live in pure Dart use-case classes, fully covered by unit tests. Cloud Functions must be written in TypeScript and covered by Jest unit tests. Overall test coverage must reach a minimum of 80%, enforced in CI/CD (GitHub Actions with Flutter test and Firebase Emulator Suite).

---

## Architecture

### 1) Flutter + Firebase (BaaS — Backend as a Service)

#### System diagram

```
┌────────────────────────────────────────────────────────────────────┐
│                Flutter Application (Mobile / Web)                  │
│                                                                    │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  Presentation Layer (Widgets, Pages, Riverpod / BLoC)       │   │
│  └──────────────────────────┬──────────────────────────────────┘   │
│                             │                                      │
│  ┌──────────────────────────▼──────────────────────────────────┐   │
│  │  Domain Layer (Use Cases, Entities, Repository Interfaces)  │   │
│  └──────────────────────────┬──────────────────────────────────┘   │
│                             │                                      │
│  ┌──────────────────────────▼──────────────────────────────────┐   │
│  │  Data Layer (Firebase Repository Implementations, DTOs)     │   │
│  └──────────────────────────┬──────────────────────────────────┘   │
└────────────────────────────────────────────────────────────────────┘
                              │  Firebase SDK (Dart / FlutterFire)
              ┌───────────────┼──────────────────────────┐
              │               │                          │
   ┌──────────▼──┐  ┌─────────▼──────────┐  ┌───────────▼────────┐
   │  Firebase   │  │  Cloud Firestore    │  │  Firebase Storage  │
   │  Auth       │  │  (real-time DB)     │  │  (documents, imgs) │
   └─────────────┘  └─────────┬──────────┘  └────────────────────┘
                              │ triggers
                   ┌──────────▼──────────────┐
                   │  Cloud Functions         │
                   │  (TypeScript)            │
                   │  - matchDonors()         │
                   │  - expireRequests()      │
                   │  - recalcEligibility()   │
                   └──────────┬──────────────┘
                              │
              ┌───────────────┼──────────────┐
   ┌──────────▼──┐  ┌─────────▼──────┐  ┌───▼──────────────┐
   │  FCM        │  │  Email         │  │  Cloud Scheduler │
   │  (push)     │  │  (SendGrid)    │  │  (cron jobs)     │
   └─────────────┘  └────────────────┘  └──────────────────┘
```

All backend logic lives in Firebase managed services. The Flutter app communicates directly with Firebase via the Dart SDK (FlutterFire). Cloud Functions act as the server-side processing layer for matching, scheduling, and notification dispatch.

**Key components:**
- **Flutter (Dart)** — cross-platform UI for Android, iOS, and Web from a single codebase
- **Riverpod / BLoC** — state management and dependency injection in the Flutter app
- **Firebase Authentication** — user registration, login, and Google OAuth; issues short-lived JWTs automatically
- **Cloud Firestore** — real-time NoSQL database storing users, donor profiles, blood requests, matches, and donation history; Firestore listeners push updates to the app instantly
- **Cloud Functions (TypeScript)** — server-side logic triggered by Firestore writes or Cloud Scheduler: `matchDonors()`, `expireRequests()`, `recalcEligibility()`
- **Firebase Cloud Messaging (FCM)** — push notifications to matched donors on Android, iOS, and Web
- **Firebase Storage** — institution verification documents and donor profile photos
- **Cloud Scheduler** — triggers nightly eligibility recalculation and request expiry checks
- **Hive / flutter_cache_manager** — local on-device caching of compatibility tables and recent request lists

#### Advantages

- **Zero server management** — Firebase handles provisioning, scaling, and uptime; the team focuses entirely on Flutter UI and Dart business logic.
- **Real-time out of the box** — Firestore's real-time listeners deliver blood request status changes to all active Flutter clients instantly, with no WebSocket configuration required.
- **Single language for client logic** — all domain and data layer code is written in Dart; business rules (eligibility, compatibility) are pure Dart classes that are trivial to unit test.
- **Rapid development** — Firebase Auth, Firestore, Storage, and FCM are all available as first-party Flutter packages (FlutterFire); integration requires minimal boilerplate.
- **Automatic scalability** — Firestore and Cloud Functions scale horizontally without any infrastructure changes.
- **Cross-platform from one codebase** — the same Flutter app runs on Android, iOS, and Web, reducing development and maintenance cost.
- **Built-in offline support** — Firestore's offline persistence allows donors to view their history and respond to match requests even with intermittent connectivity.

#### Disadvantages

- **Vendor lock-in** — the entire backend is tied to Google Firebase; migrating to a self-hosted solution requires rewriting all data access and auth layers.
- **Limited server-side control** — complex matching logic that requires joins or aggregations across large datasets can be slow or expensive in Firestore; it cannot replace a relational database for advanced analytics.
- **Cloud Function cold starts** — the `matchDonors()` function may experience cold-start latency (up to 2–3 seconds) after an idle period, causing delayed notifications for the first critical request.
- **Firestore query constraints** — Firestore does not support native complex geospatial queries; a library such as GeoFlutterFire or a pre-computed geohash field is required, adding implementation complexity.
- **Cost unpredictability** — Firestore pricing is per read/write operation; a high-volume notification flow with many active donors can generate significant costs without careful data modeling.

---

### 2) Flutter + Custom REST API backend (Dart Frog / Node.js)

#### System diagram

```
┌────────────────────────────────────────────────────────────────────┐
│                Flutter Application (Mobile / Web)                  │
│                                                                    │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  Presentation Layer (Widgets, Pages, Riverpod / BLoC)       │   │
│  └──────────────────────────┬──────────────────────────────────┘   │
│                             │                                      │
│  ┌──────────────────────────▼──────────────────────────────────┐   │
│  │  Domain Layer (Use Cases, Entities, Repository Interfaces)  │   │
│  └──────────────────────────┬──────────────────────────────────┘   │
│                             │                                      │
│  ┌──────────────────────────▼──────────────────────────────────┐   │
│  │  Data Layer (HTTP Repository Implementations, DTOs)         │   │
│  └──────────────────────────┬──────────────────────────────────┘   │
└────────────────────────────────────────────────────────────────────┘
                              │  HTTPS / WebSocket
                              ▼
              ┌───────────────────────────────┐
              │     REST API Backend          │
              │     (Dart Frog or Node.js)    │
              │                               │
              │  ┌────────────────────────┐   │
              │  │  Routes + Controllers  │   │
              │  └───────────┬────────────┘   │
              │              │                │
              │  ┌───────────▼────────────┐   │
              │  │  MatchingService       │   │
              │  │  EligibilityService    │   │
              │  │  NotificationService   │   │
              │  └──┬────────┬────────┬───┘   │
              └─────┼────────┼────────┼───────┘
                    │        │        │
            ┌───────▼──┐ ┌───▼───┐ ┌──▼──────────┐
            │PostgreSQL│ │ Redis │ │ S3 Storage  │
            │+ PostGIS │ │       │ │             │
            └──────────┘ └───────┘ └─────────────┘
                                        │
                        ┌───────────────▼──────────────┐
                        │  FCM (Firebase Admin SDK)    │
                        │  WebSocket (real-time push)  │
                        └──────────────────────────────┘
```

The Flutter app communicates with a custom REST API backend over HTTPS. The backend owns all business logic, database access, and notification dispatch. Real-time updates are delivered via WebSockets or server-sent events from the backend.

**Key components:**
- **Flutter (Dart)** — cross-platform UI communicating with the backend via the `dio` package; WebSocket updates via `web_socket_channel`
- **Dart Frog or Node.js (Express / Fastify)** — REST API handling all business logic: matching, eligibility, request lifecycle
- **MatchingService** — encapsulated service implementing ABO/Rh compatibility and PostGIS-based geolocation donor filtering
- **JWT authentication** — short-lived access tokens issued and validated by the backend; refresh token rotation
- **PostgreSQL + PostGIS** — relational database with native geospatial support for efficient radius-based donor queries
- **Redis** — caching of compatibility tables, active request snapshots, and background job queuing
- **FCM Server SDK (Firebase Admin)** — backend sends push notifications to Flutter clients via the Firebase Admin SDK without locking in the full Firebase BaaS
- **S3-compatible storage** — institution documents and profile images
- **WebSocket server** — pushes real-time request status changes to connected Flutter clients

#### Advantages

- **Full backend control** — complex matching logic, advanced geospatial queries (PostGIS), and analytics SQL can be implemented without the constraints of a NoSQL database.
- **Predictable costs** — self-hosted PostgreSQL and Redis have fixed infrastructure costs, avoiding per-operation Firestore billing.
- **No vendor lock-in** — the backend can be hosted on any cloud provider or on-premise server; migrating is a deployment concern, not a code rewrite.
- **Dart consistency (with Dart Frog)** — the entire stack can be written in Dart, allowing shared DTOs, validation logic, and compatibility rules via a common Dart package used by both the Flutter client and the server.
- **Relational integrity** — donation history, eligibility records, and match logs can be managed with foreign keys, transactions, and SQL aggregations — ideal for audit trails in a medical context.
- **Advanced analytics** — complex reporting (match rates by blood type, regional coverage gaps) is straightforward with SQL, unlike Firestore aggregations.

#### Disadvantages

- **Server management required** — the team must provision, secure, and maintain servers, databases, and Redis instances; DevOps knowledge is needed.
- **More boilerplate** — auth, WebSocket handling, push notification integration, and queue setup must all be implemented manually, unlike Firebase's plug-and-play SDKs.
- **Harder local development** — developers need PostgreSQL, Redis, and the API server all running locally to test end-to-end flows.
- **Real-time complexity** — implementing real-time updates requires a dedicated WebSocket layer or server-sent events, whereas Firestore provides this out of the box.
- **Slower initial delivery** — more infrastructure to set up before the first feature can be tested end-to-end.

---

### Recommendation: Option A — Flutter + Firebase

**Option A is the better choice for this project at this stage.**

The core reasons are:

1. **Real-time matching is the heart of the product** — Firestore's real-time listeners deliver instant request status changes to all Flutter clients without any custom WebSocket infrastructure. This is the most important technical requirement of a blood donor service, and Firebase solves it with zero server-side code.

2. **Flutter and Firebase are a first-class pair** — the FlutterFire plugin suite (`firebase_auth`, `cloud_firestore`, `firebase_messaging`, `firebase_storage`) is officially maintained by Google and the Flutter team. Integration is minimal, stable, and well-documented — the team writes Dart, not infrastructure YAML.

3. **Zero ops overhead** — for a service where uptime is critical (a missed notification in a critical blood request has real consequences), delegating availability, scaling, and backups to Firebase's managed infrastructure is a strong risk-reduction strategy.

4. **Cross-platform reach** — a blood donor service benefits from maximum reach. Flutter + Firebase delivers identical functionality on Android, iOS, and Web from a single Dart codebase, maximising the potential donor pool without multiplying development cost.

5. **Offline-first resilience** — Firestore's offline persistence means a donor in a low-connectivity area can still view and respond to a match request; the response syncs automatically when connectivity resumes. This is a meaningful feature for a medical service operating in regions with unreliable networks.

6. **Migration path preserved** — Firestore data can be exported and migrated to PostgreSQL if the project outgrows its query capabilities. Cloud Functions can be progressively replaced by a custom backend behind an API layer without changing the Flutter client, as long as repository interfaces in the domain layer remain stable.

Option B becomes appropriate when the platform requires complex relational analytics across millions of records, strict data residency compliance that Firebase's regional options cannot satisfy, or when the team has dedicated DevOps capacity and needs full control over the database engine. At that point, introducing a Dart Frog or Node.js backend — while keeping FCM for push and Firebase Auth for identity — is the most pragmatic first step rather than a full Firebase removal.
