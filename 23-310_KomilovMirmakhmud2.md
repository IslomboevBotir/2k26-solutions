 Car Wash Scheduling App
 System Requirements & Architecture

---

 Stage 1 — Requirements

 Loyiha haqida
Car Wash Scheduling App** — foydalanuvchilarga onlayn ravishda avtomobil yuvish uchun uchrashuv belgilash imkonini beruvchi platforma.

---

 Functional Requirements

 | Talab | Tavsif |

User Authentication | Foydalanuvchi email yoki telefon orqali ro'yxatdan o'tishi, JWT token asosida tizimga kirishi mumkin |
Appointment Scheduling | Mijoz mavjud bo'sh vaqt slotlarini ko'rishi, sana tanlashi va xizmat turini belgilab uchrashuv yaratishi mumkin |
|Service Catalog | Administrator xizmat turlarini qo'shishi, tahrirlashi va o'chirishi mumkin (narx, davomiyligi, tavsifi) |
Online Payment | Foydalanuvchi Payme yoki Click orqali oldindan to'lov qila olishi kerak |
Notifications | Tizim uchrashuv yaratilganda, 1 soat oldin va bekor qilinganda SMS va Push-notification yuborishi kerak |

---

 Non-Functional Requirements

Talab | Metrika |

Performance | API so'rovlari 300ms ichida javob qaytarishi, 500+ concurrent user |
Availability | 99.5% uptime (yiliga ≈ 44 soat ruxsat etilgan to'xtash) |
Security | HTTPS, bcrypt, PCI DSS, SQL Injection va XSS himoyasi |
Scalability | 10x foydalanuvchi o'sishida horizontal scaling imkoniyati |
Maintainability | 80%+ test coverage, Docker, CI/CD pipeline, API versioning |

---

Stage 2 — Architecture

---

 Variant 1 — Monolithic Architecture


┌─────────────────────────────────────────────────────────────────┐
│                         CLIENT LAYER                            │
│                                                                 │
│   ┌──────────────┐    ┌──────────────┐    ┌──────────────┐     │
│   │  Web Browser │    │  Mobile App  │    │ Admin Panel  │     │
│   │ React/Vue.js │    │React Native  │    │  Dashboard   │     │
│   │  Port: 443   │    │ iOS/Android  │    │  Operator    │     │
│   └──────┬───────┘    └──────┬───────┘    └──────┬───────┘     │
└──────────┼────────────────── ┼ ──────────────────┼─────────────┘
           │                   │                   │
           └───────────────────┼───────────────────┘
                               │ HTTPS
                               ▼
┌─────────────────────────────────────────────────────────────────┐
│                        GATEWAY LAYER                            │
│                                                                 │
│              ┌──────────────────────────────┐                   │
│              │      NGINX Reverse Proxy      │                   │
│              │  SSL · Static · Gzip · Limit  │                   │
│              │   Port 80 → 443 redirect      │                   │
│              └──────────────┬───────────────┘                   │
└─────────────────────────────┼───────────────────────────────────┘
                              │ HTTP / REST API
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│              APPLICATION LAYER (Node.js / Django)               │
│                                                                 │
│  ┌─────────────┐ ┌──────────────┐ ┌────────────┐ ┌──────────┐  │
│  │ Auth Module │ │  Scheduling  │ │  Payment   │ │ Notify   │  │
│  │             │ │   Module     │ │   Module   │ │ Module   │  │
│  │ JWT · bcrypt│ │Slots·Booking │ │Payme·Click │ │SMS·Push  │  │
│  │ OAuth2·RBAC │ │Availability  │ │Refund·Hist │ │Email·TPL │  │
│  └─────────────┘ └──────────────┘ └────────────┘ └──────────┘  │
│                                                                 │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │  Shared: Logger · Config · ErrorHandler · Middleware    │   │
│  └─────────────────────────────────────────────────────────┘   │
└──────────────────────────┬──────────────────────┬──────────────┘
                           │ SQL                  │ Cache
                           ▼                      ▼
┌─────────────────────────────────┐  ┌───────────────────────┐
│       DATA LAYER                │  │      Redis Cache       │
│                                 │  │                       │
│  ┌─────────────────────────┐    │  │  Sessions · Slots     │
│  │   PostgreSQL Database   │    │  │  Rate limit · Queue   │
│  │                         │    │  └───────────────────────┘
│  │ users · appointments    │    │
│  │ services · payments     │    │
│  │ notifications · slots   │    │
│  └─────────────────────────┘    │
└─────────────────────────────────┘
                  │ (dashed = external API calls)
      ┌───────────┼───────────┬──────────────┬───────────┐
      ▼           ▼           ▼              ▼           ▼
┌─────────┐ ┌─────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐
│  Payme  │ │  Click  │ │  Eskiz   │ │ Firebase │ │  Google  │
│Payment  │ │Payment  │ │   SMS    │ │   FCM    │ │   Maps   │
│   GW    │ │   GW    │ │ Gateway  │ │  Push    │ │    API   │
└─────────┘ └─────────┘ └──────────┘ └──────────┘ └──────────┘

Afzalliklari
Sodda ishlab chiqish — barcha kod bir joyda
Deploy qilish oson — bitta server, bitta Docker image
Debugging qulay — loglarni kuzatish oson
Kam xarajat — bitta hosting yetarli
DB tranzaksiyalari to'liq ishlaydi

Kamchiliklari
Butun ilovani scale qilish kerak, faqat bitta modulni emas
Bitta modul crash qilsa, butun tizim ishlamaydi
Har bir o'zgarish uchun butun ilova qayta deploy qilinadi
Texnologiya bog'liqligi — barcha modul bitta tilda
Kod bazasi o'sgan sari tushunish qiyinlashadi

---

 Variant 2 — Microservices Architecture


┌─────────────────────────────────────────────────────────────────┐
│                         CLIENT LAYER                            │
│                                                                 │
│   ┌──────────────┐    ┌──────────────┐    ┌──────────────┐     │
│   │  Web Browser │    │  Mobile App  │    │ Admin Panel  │     │
│   │  React/Vue   │    │React Native  │    │  Dashboard   │     │
│   └──────┬───────┘    └──────┬───────┘    └──────┬───────┘     │
└──────────┼────────────────── ┼ ──────────────────┼─────────────┘
           └───────────────────┼───────────────────┘
                               │ HTTPS
                               ▼
┌─────────────────────────────────────────────────────────────────┐
│                       API GATEWAY LAYER                         │
│                                                                 │
│         ┌──────────────────────────────────────┐               │
│         │   Kong / AWS API Gateway / Traefik    │               │
│         │  Auth · Rate limit · Routing · SSL    │               │
│         │  Load balancing · Logging · Metrics   │               │
│         └──────┬──────────┬──────────┬──────────┘               │
└────────────────┼──────────┼──────────┼──────────────────────────┘
                 │          │          │
    ┌────────────┘    ┌─────┘    ┌─────┘──────────┐
    │ REST            │ REST     │ REST            │ REST
    ▼                ▼          ▼                 ▼
┌──────────────────────────────────────────────────────────────────┐
│                      MICROSERVICES LAYER                         │
│                                                                  │
│ ┌────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐  │
│ │Auth Service│ │ Scheduling  │ │  Payment    │ │Notification │  │
│ │            │ │   Service   │ │   Service   │ │   Service   │  │
│ │ Node.js    │ │ Python/Djng │ │  Node.js    │ │  Go (lang)  │  │
│ │ Port: 3001 │ │ Port: 3002  │ │  Port: 3003 │ │  Port: 3004 │  │
│ │            │ │             │ │             │ │             │  │
│ │JWT · OAuth2│ │Slots·Book   │ │Payme·Click  │ │SMS · Push   │  │
│ │bcrypt·RBAC │ │Availability │ │Refund·Hook  │ │Email · TPL  │  │
│ └─────┬──────┘ └──────┬──────┘ └──────┬──────┘ └──────┬──────┘  │
└───────┼───────────────┼───────────────┼───────────────┼──────────┘
        │               │ publish       │ publish       │ subscribe
        │        ┌──────┴───────────────┴───────┐       │
        │        │                               │       │
        │        ▼                               │       │
        │ ┌─────────────────────────────────┐   │       │
        │ │      MESSAGE BROKER LAYER        │   │       │
        │ │   RabbitMQ / Apache Kafka        │◄──┘       │
        │ │                                  │───────────┘
        │ │ Queues:                          │
        │ │  booking.created                 │
        │ │  payment.confirmed               │
        │ │  appointment.reminder            │
        │ │  booking.cancelled               │
        │ └──────────────────────────────────┘
        │
        │         DATABASE PER SERVICE PATTERN
        ▼               ▼               ▼               ▼
┌────────────┐ ┌──────────────┐ ┌────────────┐ ┌────────────────┐
│  Auth DB   │ │ Schedule DB  │ │ Payment DB │ │   Notify DB    │
│            │ │              │ │            │ │                │
│ PostgreSQL │ │  PostgreSQL  │ │ PostgreSQL │ │ Redis + Mongo  │
│            │ │              │ │            │ │                │
│ users      │ │ slots        │ │transactions│ │ logs           │
│ roles      │ │ bookings     │ │ invoices   │ │ templates      │
│ tokens     │ │ services     │ │ refunds    │ │ queue          │
└────────────┘ └──────────────┘ └────────────┘ └────────────────┘
                                      │                  │
             ┌────────────────────────┘                  │
             ▼                                           ▼
┌────────────────────────────────┐    ┌──────────────────────────┐
│      EXTERNAL PAYMENTS         │    │   EXTERNAL NOTIFICATIONS  │
│                                │    │                          │
│  ┌──────────┐  ┌──────────┐    │    │  ┌──────────┐ ┌───────┐  │
│  │  Payme   │  │  Click   │    │    │  │  Eskiz   │ │ FCM   │  │
│  │  API     │  │  API     │    │    │  │  SMS GW  │ │ Push  │  │
│  └──────────┘  └──────────┘    │    │  └──────────┘ └───────┘  │
└────────────────────────────────┘    └──────────────────────────┘

┌─────────────────────────────────────────────────────────────────┐
│                    INFRASTRUCTURE / DEVOPS                      │
│                                                                 │
│  ┌───────────┐  ┌───────────┐  ┌──────────────┐  ┌─────────┐  │
│  │Kubernetes │  │  Docker   │  │  CI/CD       │  │Monitorng│  │
│  │           │  │           │  │              │  │         │  │
│  │Auto-scale │  │Container  │  │GitHub Actions│  │Promethus│  │
│  │Self-heal  │  │Registry   │  │Test→Build    │  │Grafana  │  │
│  │Rolling    │  │Compose    │  │→Deploy       │  │Jaeger   │  │
│  │deploy     │  │(dev)      │  │Per-service   │  │ELK Stack│  │
│  └───────────┘  └───────────┘  └──────────────┘  └─────────┘  │
└─────────────────────────────────────────────────────────────────┘
```

Afzalliklari
 Mustaqil kengayish — faqat yuklangan servis scale qilinadi
 Fault isolation — bitta servis tushsa, qolganlari ishlayveradi
 Texnologiya erkinligi — har bir servis boshqa tilda yozilishi mumkin
 Mustaqil deploy — har bir servis alohida CI/CD pipeline
 Katta jamoalar uchun ideal

 Kamchiliklari
 Murakkab infratuzilma — Kubernetes, API Gateway, Message Broker kerak
 Distributed tracing qiyin — xatolarni bir nechta servis orqali kuzatish
 Network latency — servislar o'rtasida HTTP/gRPC chaqiruvlar vaqt oladi
 Ma'lumotlar konsistentligi — Saga pattern kerak
 Yuqori xarajat — ko'proq server va DevOps bilimlari talab etiladi

---

Qaysi biri yaxshiroq?

Tavsiya: Monolithic (boshlang'ich bosqich uchun)**

| Mezon | Monolith | Microservices |

| Ishlab chiqish tezligi | Tez |  Sekin |
| Xarajat | Arzon |  Qimmat |
| Kengayish |  Cheklangan |  Kuchli |
| Fault Tolerance |  Past |  Yuqori |
| Murakkablik |  Oddiy |  Murakkab |

Strategiya: Monolith bilan boshlash, keyin foydalanuvchilar 10,000+ bo'lganda Microservices ga o'tish.
 Bu yondashuv Shopify, Airbnb, Uber kabi kompaniyalarda qo'llanilgan.

---

Muallif: [23-310] [Komilov] [Mirmakhmud]*

