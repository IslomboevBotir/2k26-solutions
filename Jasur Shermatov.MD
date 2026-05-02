# Talabalar Stipendiya Portali — Tizim Talablari va Arxitekturasi

**Loyiha:** Student Scholarship Portal  
**Mavzu:** Tizim talablari va arxitekturasi  

---

## 1-bosqich. Talablar

### Funksional Talablar

| # | Talab | Tavsif |
|---|-------|--------|
| FR-1 | **Foydalanuvchi autentifikatsiyasi** | Talabalar, professor-o'qituvchilar va administrator roli bo'yicha tizimga kirish imkoniyati. Email/parol yoki universitet SSO orqali login. |
| FR-2 | **Stipendiyaga ariza topshirish** | Talaba stipendiya turlarini ko'rishi, shartlarni o'qishi va elektron ariza to'ldirishi mumkin. Hujjatlarni (PDF, JPG) biriktirish imkoniyati mavjud. |
| FR-3 | **Ariza holatini kuzatish** | Talaba o'z arizasining joriy holatini (yuborilgan → ko'rib chiqilmoqda → tasdiqlangan / rad etilgan) real vaqt rejimida kuzatishi mumkin. |
| FR-4 | **Administrator paneli** | Admin yangi stipendiya dasturlarini yaratishi, arizalarni ko'rib chiqishi, tasdiqlashi yoki rad etishi, g'oliblarni e'lon qilishi mumkin. |
| FR-5 | **Bildirishnomalar tizimi** | Ariza holati o'zgarganda talabaga avtomatik email va/yoki portal ichida bildirishnoma yuboriladi. |

---

### Nofunksional Talablar

| # | Talab | Mezon |
|---|-------|-------|
| NFR-1 | **Ishonchlilik (Availability)** | Tizim yillik 99.5% uptime darajasida ishlashi kerak. Rejalashtirilgan texnik xizmat vaqtidan tashqari uzilishlar 15 daqiqadan oshmasligi lozim. |
| NFR-2 | **Ishlash unumdorligi (Performance)** | Har qanday sahifa 3 soniya ichida yuklanishi kerak. Arizalar ro'yxati so'rovlari 1000 ta bir vaqtdagi foydalanuvchi sharoitida 2 soniyadan oshmasligi lozim. |
| NFR-3 | **Xavfsizlik (Security)** | Barcha ma'lumotlar HTTPS orqali uzatilishi, parollar bcrypt algoritmi bilan shifrlanishi va SQL injection, XSS hujumlariga qarshi himoya mavjud bo'lishi shart. |
| NFR-4 | **Kengayuvchanlik (Scalability)** | Tizim 10 000 gacha bir vaqtdagi foydalanuvchiga xizmat ko'rsatishga mo'ljallangan bo'lishi va talabni oshirish vaqtida gorizontal scaling qo'llab-quvvatlashi kerak. |
| NFR-5 | **Foydalanish qulayligi (Usability)** | Interfeys mobil qurilmalar bilan mos (responsive design) bo'lishi va asosiy harakatlarni 3 ta klik ichida amalga oshirish imkonini berishi kerak. |

---

## 2-bosqich. Arxitektura

### Variant 1 — Monolitik Arxitektura (MVC)

#### Tizim sxemasi

```
┌─────────────────────────────────────────────────────────┐
│                      Foydalanuvchi                      │
│              (Web Browser / Mobile Browser)             │
└──────────────────────┬──────────────────────────────────┘
                       │ HTTPS
┌──────────────────────▼──────────────────────────────────┐
│                   Nginx (Reverse Proxy)                 │
│               Static fayllarni xizmat qiladi            │
└──────────────────────┬──────────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────────┐
│           Monolitik Ilova Serveri (Django / Laravel)    │
│  ┌──────────────┐  ┌──────────────┐  ┌───────────────┐  │
│  │  Auth Module │  │  Ariza Modul │  │  Admin Modul  │  │
│  └──────────────┘  └──────────────┘  └───────────────┘  │
│  ┌──────────────┐  ┌──────────────┐                     │
│  │  Bildirishnoma│  │  Fayl Yuklash│                     │
│  │     Modul    │  │    Modul     │                     │
│  └──────────────┘  └──────────────┘                     │
└──────────────────────┬──────────────────────────────────┘
                       │
          ┌────────────┴────────────┐
          │                        │
┌─────────▼──────────┐   ┌─────────▼──────────┐
│  PostgreSQL (asosiy│   │  File Storage       │
│     ma'lumotlar    │   │  (MinIO / Disk)     │
│       bazasi)      │   └────────────────────┘
└────────────────────┘
```

#### Afzalliklari

- **Oddiy ishlab chiqish:** Butun loyiha bitta repozitoriyada, yangi dasturchilar tezda boshlay oladi.
- **Tez prototiplash:** MVC freymvorklari (Django, Laravel) standart funksiyalarni tezda yozish imkonini beradi.
- **Kam infrastruktura xarajati:** Bitta server va bitta ma'lumotlar bazasi yetarli; DevOps murakkabligi past.
- **Oson nosozliklarni tuzatish:** Barcha kod bir joyda bo'lgani uchun stack trace to'liq va aniq bo'ladi.

#### Kamchiliklari

- **Kengayuvchanlik chegarasi:** Faqat vertikal scaling mumkin (serverga RAM/CPU qo'shish), gorizontal scaling qiyin.
- **Bir nuqtai nosozlik (SPOF):** Ilova serveri ishdan chiqsa, butun tizim to'xtaydi.
- **Deploy qiyinligi:** Kichik o'zgarish ham butun ilovani qayta deploy qilishni talab qiladi.
- **Texnologiya bog'liqligi:** Bitta dasturlash tilida yozilganligi sababli qismlarga alohida texnologiya qo'llanib bo'lmaydi.

---

### Variant 2 — Mikroservislar Arxitekturasi

#### Tizim sxemasi

```
┌─────────────────────────────────────────────────────────┐
│                      Foydalanuvchi                      │
│              (Web Browser / Mobile Browser)             │
└──────────────────────┬──────────────────────────────────┘
                       │ HTTPS
┌──────────────────────▼──────────────────────────────────┐
│                  API Gateway (Kong / Nginx)              │
│         Autentifikatsiya tekshiruvi, Rate Limiting      │
└────┬─────────┬─────────────┬──────────────┬─────────────┘
     │         │             │              │
┌────▼───┐ ┌──▼──────┐ ┌────▼──────┐ ┌────▼──────────┐
│  Auth  │ │ Ariza   │ │ Bildirishn│ │  Fayl         │
│ Servis │ │ Servisi │ │ oma Servis│ │  Servisi      │
│(Node)  │ │(Python) │ │ (Node)   │ │ (Go)          │
└────┬───┘ └──┬──────┘ └────┬──────┘ └────┬──────────┘
     │        │             │             │
┌────▼───┐ ┌──▼──────┐ ┌────▼──────┐ ┌────▼──────────┐
│  Users │ │Arizalar │ │  Message  │ │  Object       │
│   DB   │ │   DB    │ │  Broker   │ │  Storage      │
│(Postgres│ │(Postgres│ │(RabbitMQ) │ │  (MinIO/S3)   │
└────────┘ └─────────┘ └───────────┘ └───────────────┘
```

#### Afzalliklari

- **Mustaqil kengayuvchanlik:** Faqat yuklangan servisni (masalan, ariza servisi) alohida scale qilish mumkin.
- **Yuqori fault tolerance:** Bir servis ishdan chiqsa, boshqalari ishlashda davom etadi.
- **Texnologiya erkinligi:** Har bir servis uchun eng mos til va freymvork tanlanishi mumkin.
- **Mustaqil deploy:** Kichik o'zgarish faqat tegishli servisni qayta deploy qilishni talab qiladi, bu production riskini kamaytiradi.

#### Kamchiliklari

- **Murakkab infrastruktura:** Kubernetes, Docker, API Gateway, Message Broker — bularni sozlash va boshqarish tajriba talab qiladi.
- **Tarmoq kengligi xarajati:** Servislar o'rtasidagi HTTP/gRPC chaqiruvlari kechikish (latency) va nosozlik ehtimolini oshiradi.
- **Distributed tracing qiyinligi:** Xatoliklarni bir nechta servis loglaridan kuzatish murakkablashadi.
- **Yuqori boshlang'ich xarajat:** Dastlabki sozlash va ishlab chiqish vaqti ko'proq sarflanadi.

---

### Qaysi biri yaxshiroq va nima uchun?

**Ushbu loyiha uchun tavsiya: Monolitik Arxitektura (Variant 1)**

**Asoslar:**

1. **Loyiha miqyosi mosdir.** Talabalar stipendiya portali katta korporativ tizim emas — foydalanuvchilar soni va funksiyalar chegarali. Monolitik yondashuv bu miqyos uchun ortiqcha murakkablikni oldini oladi.

2. **Resurs tejamkorligi.** Mikroservislar uchun DevOps mutaxassisi, Kubernetes klaster, bir nechta ma'lumotlar bazasi va monitoring infrastrukturasi kerak — bu qimmat va vaqt talab etadi.

3. **Tezkor ishlab chiqish.** MVP (minimal viable product) tezroq tayyorlanishi zarur bo'lsa, monolitik yondashuv ancha samarali.

4. **Keyingi o'sish imkoniyati.** Agar kelajakda tizim juda katta o'ssa, "Modular Monolith" pattern orqali qismlarni asta-sekin mikroservislarga ajratish mumkin — bu "strangler fig pattern" deyiladi.

> **Xulosa:** Loyiha boshlang'ich bosqichida Variant 1 (Monolitik) bilan boshlanishi, kerak bo'lganda Variant 2 (Mikroservislar) ga evolyutsion tarzda o'tish tavsiya etiladi.

---

*Hujjat tayyorlandi: 2026-yil, 2-may*