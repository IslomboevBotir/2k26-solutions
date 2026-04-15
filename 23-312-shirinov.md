# Smart Classroom Dashboard — Tizim Talablari va Arxitekturasi

---

## 1-bosqich. Talablar

### Loyiha mavzusi: Smart Classroom Dashboard

Smart Classroom Dashboard — bu o'quv muassasalarida darsxona faoliyatini real vaqtda kuzatish, boshqarish va tahlil qilish imkonini beruvchi raqamli boshqaruv tizimi.

---

### Funksional talablar

| # | Talab |
|---|-------|
| 1 | **Real vaqtda monitoring** — Tizim darsxonadagi harorat, yorug'lik darajasi, CO₂ miqdori va band/bo'sh holatini real vaqtda ko'rsatishi kerak. |
| 2 | **Dars jadvali integratsiyasi** — Tizim dars jadvalini avtomatik yuklashi va joriy dars haqidagi ma'lumotni (fan, o'qituvchi, guruh) dashboardda aks ettirishi kerak. |
| 3 | **Davomat hisobi** — Tizim RFID karta yoki yuz tanish orqali talabalar va o'qituvchilarning davomatini avtomatik qayd etishi kerak. |
| 4 | **Qurilmalarni masofadan boshqarish** — Administrator proyektor, konditsioner va aqlli taxtani dashboarddan yoqish/o'chirish imkoniga ega bo'lishi kerak. |
| 5 | **Hisobot va tahlil** — Tizim haftalik/oylik statistika hisobotlarini (darsxona bandligi, energiya sarfi, davomat foizi) PDF yoki Excel formatida yaratishi kerak. |

---

### Nofunksional talablar

| # | Talab |
|---|-------|
| 1 | **Ishlash tezligi (Performance)** — Sensor ma'lumotlari 2 soniya ichida dashboardda yangilanishi kerak; sahifa yuklanish vaqti 3 soniyadan oshmasligi kerak. |
| 2 | **Xavfsizlik (Security)** — Foydalanuvchi ma'lumotlari AES-256 bilan shifrlangan holda saqlanishi va JWT token orqali autentifikatsiya ta'minlanishi kerak. |
| 3 | **Mavjudlik (Availability)** — Tizim 99.5% uptime kafolati bilan ishlashi, texnik xizmat vaqtida ham asosiy funksiyalar foydalanuvchiga ko'rinishi kerak. |
| 4 | **Kengayuvchanlik (Scalability)** — Tizim bitta muassasada 100 tagacha darsxonani, tarmoqda esa 50 tagacha fillialni qo'llab-quvvatlashi kerak. |
| 5 | **Foydalanish qulayligi (Usability)** — Interfeys mobil va desktop qurilmalarda to'g'ri ko'rinishi (responsive design), texnik bo'lmagan foydalanuvchilar uchun ham tushinarli bo'lishi kerak. |

---

## 2-bosqich. Arxitektura

---

### Variant 1: Monolitik Arxitektura (Monolithic Architecture)

#### Tizim sxemasi

```
┌─────────────────────────────────────────────────┐
│               FOYDALANUVCHI (Browser/App)        │
└──────────────────────┬──────────────────────────┘
                       │ HTTP/HTTPS
┌──────────────────────▼──────────────────────────┐
│                                                 │
│         MONOLITIK SERVER (Node.js / Django)     │
│  ┌─────────────┐  ┌──────────────┐  ┌────────┐ │
│  │  Auth modul │  │ Dars jadvali │  │Hisobot │ │
│  └─────────────┘  └──────────────┘  └────────┘ │
│  ┌─────────────┐  ┌──────────────┐             │
│  │  Davomat    │  │ Qurilma mgmt │             │
│  └─────────────┘  └──────────────┘             │
│                                                 │
└──────────────────────┬──────────────────────────┘
                       │
        ┌──────────────▼──────────────┐
        │    PostgreSQL (Yagona DB)   │
        └─────────────────────────────┘
                       │
        ┌──────────────▼──────────────┐
        │     IoT Sensor Gateway      │
        │  (MQTT broker — Mosquitto)  │
        └─────────────────────────────┘
```

#### Afzalliklari
- **Oddiy ishlab chiqish** — Barcha kodlar bir joyda, kichik jamoa uchun qulay.
- **Tez prototiplash** — MVP ni qisqa vaqtda yaratish mumkin.
- **Deployment oson** — Bitta server, bitta deploy jarayoni.
- **Debugging oson** — Muammoni bir joyda qidirish kifoya.

#### Kamchiliklari
- **Kengayish qiyin** — Foydalanuvchi soni o'sishi bilan butun tizimni kengaytirish kerak bo'ladi.
- **Bir xato — hammasi to'xtaydi** — Bitta modul ishdan chiqsa, butun tizim ishlamay qoladi.
- **Texnologiya bog'liqligi** — Bitta dasturlash tili/framework bilan cheklanib qolinadi.
- **CI/CD murakkabligi** — Kichik o'zgarish uchun ham butun tizimni qayta deploy qilish kerak.

---

### Variant 2: Mikroservis Arxitektura (Microservices Architecture)

#### Tizim sxemasi

```
┌─────────────────────────────────────────────────────┐
│               FOYDALANUVCHI (Browser/App)            │
└────────────────────────┬────────────────────────────┘
                         │ HTTPS
┌────────────────────────▼────────────────────────────┐
│              API GATEWAY (Kong / Nginx)              │
│         (Auth, Rate limiting, Load balancing)        │
└──┬──────────┬───────────┬────────────┬──────────────┘
   │          │           │            │
┌──▼──┐  ┌───▼───┐  ┌────▼────┐  ┌───▼──────┐
│Auth │  │Jadval │  │Davomat  │  │ Qurilma  │
│Serv.│  │Serv.  │  │Servisi  │  │ Servisi  │
└──┬──┘  └───┬───┘  └────┬────┘  └───┬──────┘
   │         │            │           │
┌──▼──┐  ┌───▼───┐  ┌────▼────┐  ┌───▼──────┐
│ DB  │  │  DB   │  │   DB    │  │    DB    │
│(PG) │  │ (PG)  │  │ (PG)   │  │  (PG)   │
└─────┘  └───────┘  └─────────┘  └──────────┘
                         │
┌────────────────────────▼────────────────────────────┐
│          MESSAGE BROKER (RabbitMQ / Kafka)           │
└────────────────────────┬────────────────────────────┘
                         │
┌────────────────────────▼────────────────────────────┐
│              IoT Sensor Gateway (MQTT)               │
│          + Real-time Service (WebSocket)             │
└─────────────────────────────────────────────────────┘
```

#### Afzalliklari
- **Mustaqil kengayish** — Faqat kerakli servisni (masalan, davomat) alohida kengaytirish mumkin.
- **Yuqori barqarorlik** — Bitta servis ishdan chiqsa, qolganlar ishlashda davom etadi.
- **Texnologiya erkinligi** — Har bir servis turli tilda yozilishi mumkin.
- **Tezkor deploy** — Har bir servis mustaqil deploy qilinadi, risk kamayadi.

#### Kamchiliklari
- **Murakkab boshqaruv** — Ko'plab servislarni monitoring qilish va boshqarish qiyin.
- **Network latency** — Servislar o'rtasidagi muloqot vaqt oladi.
- **Ishlab chiqish narxi yuqori** — Katta jamoa va ko'proq DevOps bilim talab etiladi.
- **Ma'lumotlar izchilligi** — Distributed DB'larda tranzaktsiyalarni boshqarish murakkab.

---

## Qaysi arxitektura yaxshiroq va nima uchun?

### ✅ Tavsiya: **Mikroservis Arxitektura**

Smart Classroom Dashboard uchun mikroservis arxitekturasi tanlanishi maqsadga muvofiqdir. Quyidagi sabablar bilan asoslanadi:

1. **Real vaqt talabi** — IoT sensorlar doimiy ma'lumot yuboradi. Mikroservisda real-time servisini alohida ajratib, boshqa modullarni sekinlashtirmasdan ishlash mumkin.

2. **Mustaqil modullar** — Davomat tizimi, jadval va qurilma boshqaruvi bir-biridan mustaqil ishlashi kerak. Monolitda biri ishlamasa, hammasi to'xtaydi.

3. **Kengayuvchanlik** — Kelajakda 50+ filialga tarqalish rejalashtirilgan. Mikroservis bu holatda faqat zarur servislarni kengaytirish imkonini beradi.

4. **Texnologiya moslashuvchanligi** — IoT integratsiyasi uchun Python, frontend uchun React, hisobot uchun alohida servis — barchasi birga ishlay oladi.


