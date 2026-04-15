# Event Ticket Booking Service

<br>

## 1-BOSQICH: TALABLAR
### 5 ta Funksional

| № | Talab | Tavsif |
|---|-------|--------|
| 1 | **Foydalanuvchi roʻyxatdan oʻtishi va kirishi** | Foydalanuvchilar email/telefon orqali roʻyxatdan oʻtadi, login qiladi, profilini boshqaradi |
| 2 | **Tadbirlarni koʻrish va qidirish** | Foydalanuvchilar kategoriyalar, sana, joylashuv boʻyicha tadbirlarni filtrlaydi va qidiradi |
| 3 | **Chiptalarni tanlash va bron qilish** | Foydalanuvchi oʻrindiqlarni tanlaydi, savatchaga qoʻshadi, toʻlov qiladi va elektron chipta oladi |
| 4 | **Toʻlov tizimi integratsiyasi** | Click, Payme, Stripe kabi toʻlov tizimlari orqali xavfsiz toʻlov amalga oshiriladi |
| 5 | **Admin panel** | Tadbir tashkilotchilari tadbir qoʻshadi, chipta narxlarini boshqaradi, sotilgan chiptalar statistikasini koʻradi |

<br>

### 5 ta Nofunksional

| № | Talab | Tavsif |
|---|-------|--------|
| 1 | **Masshtablanuvchanlik** | Tizim bir vaqtda 10,000+ foydalanuvchiga xizmat koʻrsata olishi kerak (koncertlar, festivallar paytida) |
| 2 | **Xavfsizlik** | Foydalanuvchi maʼlumotlari va toʻlov maʼlumotlari shifrlangan (HTTPS, JWT, PCI-DSS standartlari) |
| 3 | **Ishonchlilik** | Tizim 99.9% uptime koʻrsatkichiga ega boʻlishi, maʼlumotlar bazasi zaxira nusxasi olinishi kerak |
| 4 | **Tezkorlik** | Sahifa yuklanish vaqti ≤2 soniya, chipta bron qilish jarayoni ≤5 soniya davom etishi kerak |
| 5 | **Qulaylik (UX)** | Interfeys mobil qurilmalarga mos (responsive), oʻzbek/rus/ingliz tillarini qoʻllab-quvvatlaydi |

---

<br>

## 2-BOSQICH: ARXITEKTURA VARIANTLARI

### Variant 1: Monolit Arxitektura

```
┌─────────────────────────┐
│   Frontend (React/Vue)  │
└─────────┬───────────────┘
          │ HTTP/REST
┌─────────▼──────────────┐
│   Monolit Backend      │
│  ┌─────────────────┐   │
│  │ Auth Module     │   │
│  │ Event Module    │   │
│  │ Booking Module  │   │
│  │ Payment Module  │   │
│  └─────────────────┘   │
│         │              │
│         ▼              │
│   PostgreSQL (DB)      │
│   Redis (Cache)        │
└────────────────────────┘
```

**Afzalliklari:**
- Dasturlash va deploy qilish oson
- Bitta kod bazasi — boshqarish qulay
- Kichik jamoa va MVP uchun ideal
- Transaction boshqaruvi sodda

**Kamchiliklari:**
- Katta yuklama paytida masshtablash qiyin
- Yangi funksiyalar qoʻshishda butun tizimni qayta deploy qilish kerak
- Texnologik stack ni oʻzgartirish murakkab

---

### Variant 2: Microservices Arxitektura

```
          ┌─────────────────────────┐
          │   Frontend (React/Vue)  │
          └──────────┬──────────────┘
                     │ 
                     ▼
  ┌─────────────────────────────────────┐
  │          API Gateway                │
  │            (Nginx)                  |
  └─────────────────────────────────────┘
      │              │              │
      ▼              ▼              ▼
┌─────────────┐ ┌─────────────┐ ┌───────────┐
│ Auth Service│ │Event Service│ │Booking Svc│
│   (Java)    | |  (Python)   │ │   (Go)    │
└─────┬───────┘ └─────┬───────┘ └────┬──────┘
      │               │              │
      ▼               ▼              ▼
┌──────────┐    ┌──────────┐  ┌──────────────┐
│PostgreSQL│    │ MongoDB  │  │ Redis + Kafka│
│ (Auth)   │    │(Events)  │  │(BookingQueue)│
└──────────┘    └──────────┘  └──────────────┘
```

**Afzalliklari:**
- Har bir servis alohida masshtablanadi (masalan, Booking servisini alohida kuchaytirish)
- Har bir servis uchun optimal texnologiya tanlash mumkin
- Yangi funksiyalarni tez qoʻshish, CI/CD oson
- Xatolik bir servisdan boshqasiga tarqalmaydi

**Kamchiliklari:**
- Dasturlash va deploy murakkabroq
- Servislararo kommunikatsiya (gRPC/REST) va maʼlumotlar izchilligini boshqarish qiyin
- Monitoring, logging, tracing tizimlari talab qilinadi

---

<br>

## Qaysi Variant Yaxshiroq?

| Mezon | Monolit | Microservices |
|-------|---------|---------------|
| Loyiha hajmi | Kichik/Oʻrta | Katta/Enterprise |
| Jamoa hajmi | 3-5 dasturchi | 10+ dasturchi |
| Vaqt chegarasi | Tez MVP kerak | Uzoq muddatli rivojlanish |
| Byudjet | Cheklangan | Yetarli |

### Tavsiya: **Bosqichma-bosqich yondashuv**

1. **Boshlangʻich bosqichda (MVP)** → **Monolit** arxitekturadan boshlash maqsadga muvofiq:
   - Tezroq ishga tushirish
   - Kamroq xarajat
   - Talablarni aniqroq tushunish

2. **Tizim oʻsgach** (foydalanuvchilar soni 50k+, kunlik buyurtmalar 10k+) → **Microservices** ga migratsiya qilish:
   - Booking va Payment servislarini alohida ajratish
   - Load balancer + Kubernetes orqali avtomatik masshtablash

