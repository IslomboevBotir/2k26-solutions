🏠 Apartment Rental Platform

Kvartira ijarasi uchun zamonaviy raqamli platforma — ijara beruvchi va ijara oluvchilarni bir joyda bog'laydigan tizim.


📋 Loyiha haqida
Apartment Rental Platform — foydalanuvchilarga kvartira e'lonlarini joylash, qidirish, bron qilish va ijara jarayonini to'liq onlayn boshqarish imkonini beruvchi veb-platforma.

✅ Funksional Talablar (Functional Requirements)
FR-1 — Ro'yxatdan o'tish va autentifikatsiya
Ijara beruvchi va ijara oluvchi foydalanuvchilar:

Email/parol yoki Google/Facebook orqali tizimga kirishi
Hisobini tiklashi (parolni unutganda)
Tizimdan chiqishi mumkin bo'lishi kerak

FR-2 — Kvartira e'lon qo'shish
Mulk egasi quyidagilarni kiritib e'lon yaratishi mumkin:

Kvartira nomi, manzili, narxi
Xonalar soni, qavat, umumiy maydon (m²)
Rasmlar va video
Qo'shimcha xususiyatlar: lift, parkovka, internet, mebel va h.k.

FR-3 — Qidiruv va filtrlash
Foydalanuvchi quyidagi parametrlar bo'yicha kvartira qidira olishi kerak:

Shahar va tuman
Narx oralig'i (min–max)
Xonalar soni
Metro/manzilga yaqinlik
Natijalar real vaqt rejimida ko'rsatilishi shart

FR-4 — Ijara arizasi va bron qilish

Ijara oluvchi kvartiraga ariza yuborishi va ijara muddatini belgilashi
To'lov tizimi orqali to'lov amalga oshirishi
Mulk egasi arizani qabul yoki rad etishi mumkin bo'lishi kerak

FR-5 — Xabar almashish (In-app Chat)

Ijara oluvchi va mulk egasi platforma ichida to'g'ridan-to'g'ri yozishishi
Fayl va rasm yuborish imkoniyati
Ariza holati o'zgarganda bildirishnoma (notification) olish


⚙️ Nofunksional Talablar (Non-Functional Requirements)
NFR-1 — Ishlash tezligi (Performance)

Sahifalar 3 soniya ichida to'liq yuklanishi shart
Qidiruv natijalari 1 soniyadan oshmay ko'rsatilishi kerak
Tizim bir vaqtda 10,000 ga qadar faol foydalanuvchini ushlab turishi lozim

NFR-2 — Xavfsizlik (Security)

Foydalanuvchi ma'lumotlari AES-256 shifrlash bilan saqlanadi
To'lov tizimi PCI DSS standartiga mos bo'lishi shart
Sessiya boshqaruvi JWT token orqali amalga oshiriladi
SQL injection, XSS va CSRF hujumlaridan himoya bo'lishi kerak

NFR-3 — Mavjudlik (Availability)

Tizim yiliga 99.9% uptime bilan ishlashi shart (≤ 8.7 soat uzilish/yil)
Texnik xizmat vaqtida foydalanuvchi oldindan xabardor qilinishi kerak
Tizim uzilganda foydalanuvchiga tushunarli xato xabari ko'rsatilishi lozim

NFR-4 — Miqyoslanish (Scalability)

Foydalanuvchilar soni 5 barobarga oshganda ham tizim tezligini saqlab qolishi kerak
Ma'lumotlar bazasi va serverlar gorizontal ravishda kengaytirilishi (scale-out) mumkin bo'lishi lozim
Mikroservis arxitekturasiga o'tish imkoniyati ko'zda tutilgan bo'lishi kerak

NFR-5 — Foydalanish qulayligi (Usability)

Interfeys barcha qurilmalar uchun to'liq moslashtirilgan (responsive design) bo'lishi shart
Yangi foydalanuvchi ro'yxatdan o'tishdan birinchi arizagacha 5 daqiqa ichida bajara olishi kerak
Platforma O'zbek, Rus va Ingliz tillarini qo'llab-quvvatlashi lozim



🏗️ Tizim Arxitekturasi — Apartment Rental Platform
Loyiha uchun ikkita arxitektura varianti tahlil qilindi. Quyida har birining tizim sxemasi, afzalliklari, kamchiliklari va tavsiya keltirilgan.

Variant 1 — Monolitik Arxitektura (Monolithic)
Tizim sxemasi
┌─────────────────────────────────────────────────────────┐
│                    Foydalanuvchi                        │
└───────────────────────────┬─────────────────────────────┘
                            │ HTTP so'rov
                            ▼
┌─────────────────────────────────────────────────────────┐
│                     API Gateway                         │
└───────────────────────────┬─────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────┐
│              MONOLITIK ILOVA (bitta server)             │
│                                                         │
│  ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌───────────────┐  │
│  │  Auth   │ │ Listing │ │ Booking │ │  Chat Payment │  │
│  │ moduli  │ │ moduli  │ │ moduli  │ │  moduli moduli│  │
│  └─────────┘ └─────────┘ └─────────┘ └───────────────┘  │
│                                                         │
│        [ Umumiy kod bazasi — Shared Codebase ]          │
└───────────────────────────┬─────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────┐
│          Yagona Ma'lumotlar Bazasi (PostgreSQL)         │
│              Barcha jadvallar bir joyda                 │
└─────────────────────────────────────────────────────────┘
✅ Afzalliklari

Soddaligi — Loyihani boshlash va ishga tushirish oson. Barcha kod bitta repozitoriyada.
Tez ishlab chiqish — Dastlabki bosqichda yangi funksiyalar tez qo'shiladi.
Debugging qulayligi — Xatolarni izlash bir joyda amalga oshiriladi.
Deployment soni kam — Bitta server yoki container yetarli.
Tranzaksiyalar ishonchli — Barcha ma'lumotlar bitta bazada bo'lgani uchun ACID tranzaksiyalari muammosiz ishlaydi.
Xarajat pastligi — Kichik jamoalar uchun infratuzilma xarajatlari minimal.

❌ Kamchiliklari

Miqyoslanish muammosi — Bitta modul (masalan, Listing) yuklanib qolsa, butun tizim sekinlashadi. Faqat vertikal scaling (server kuchaytiriladi) mumkin.
Mustaqil deploy imkonsiz — Bitta modulda o'zgarish bo'lsa, butun ilova qayta deploy qilinishi kerak.
Texnologiya bog'liqligi — Barcha modullar bir dasturlash tili va freymvorkda yozilishi shart.
Kod murakkabligi o'sishi — Loyiha kattalashishi bilan kod bazasi nazorat qilish qiyinlashadi (spaghetti code).
Xato tarqalishi — Bitta modulda xato butun ilovani ishdan chiqarishi mumkin.





Variant 2 — Mikroservis Arxitektura (Microservices)
Tizim sxemasi
┌──────────────────────────────────────────────────────────────┐
│                       Foydalanuvchi                          │
└──────────────────────────────┬───────────────────────────────┘
                               │ HTTP
                               ▼
┌──────────────────────────────────────────────────────────────┐
│             API Gateway + Load Balancer                      │
│        (Nginx / Kong — routing, rate limiting, auth)         │
└────┬──────────┬──────────┬──────────┬──────────┬────────────┘
     │          │          │          │          │
     ▼          ▼          ▼          ▼          ▼
┌────────┐ ┌────────┐ ┌────────┐ ┌────────┐ ┌────────┐
│  Auth  │ │Listing │ │Booking │ │  Chat  │ │Payment │
│Service │ │Service │ │Service │ │Service │ │Service │
│        │ │        │ │        │ │        │ │        │
│JWT,    │ │E'lonlar│ │Bron,   │ │Web-    │ │To'lov  │
│OAuth2  │ │Qidiruv │ │Ariza   │ │Socket  │ │PCI DSS │
└───┬────┘ └───┬────┘ └───┬────┘ └───┬────┘ └───┬────┘
    │          │          │          │           │
    │          └──────────┼──────────┘           │
    │                     │                      │
    │              ┌──────▼──────┐               │
    │              │   Message   │               │
    └─────────────►│   Broker    │◄──────────────┘
                   │ (RabbitMQ)  │
                   └─────────────┘
    │          │          │          │           │
    ▼          ▼          ▼          ▼           ▼
┌───────┐ ┌───────┐ ┌───────┐ ┌───────┐ ┌──────────┐
│Auth DB│ │List DB│ │Book DB│ │Chat DB│ │Payment DB│
│(PgSQL)│ │(PgSQL)│ │(PgSQL)│ │(Mongo)│ │  (PgSQL) │
└───────┘ └───────┘ └───────┘ └───────┘ └──────────┘

Legenda:

──► Sinxron so'rov (HTTP/REST)
- - ► Asinxron xabar (Event / Queue)


✅ Afzalliklari

Mustaqil miqyoslanish — Faqat yuklanib qolgan servis (masalan, Listing) alohida kengaytirilishi mumkin (horizontal scaling).
Mustaqil deploy — Har bir servis boshqalardan mustaqil yangilanadi. Booking servicedagi o'zgarish faqat Booking qayta ishga tushiriladi.
Texnologiya erkinligi — Chat servisi Node.js (WebSocket uchun), Payment servisi Java, Listing servisi Python bilan yozilishi mumkin.
Xatolarga chidamlilik — Bitta servis ishlamay qolsa, boshqalari davom etaveradi (fault isolation).
Katta jamoalar uchun qulay — Har bir jamoa o'z servisini mustaqil boshqaradi.
Elastik scaling — Traffic ko'p bo'lganda aynan kerakli servislar avtomatik kengayadi (Kubernetes HPA).

❌ Kamchiliklari

Murakkab infratuzilma — Docker, Kubernetes, API Gateway, Message Broker — bularni sozlash va boshqarish qiyin.
Tarqatilgan tranzaksiyalar — Bron qilish + to'lov birgalikda muvaffaqiyatli bo'lishi uchun Saga pattern kabi murakkab yechimlar kerak.
Debugging qiyinligi — Xato qaysi servisdan chiqqanini aniqlash uchun distributed tracing (Jaeger, Zipkin) kerak.
Network latency — Servislar o'rtasida HTTP so'rovlar monolitga qaraganda sekinroq.
Xarajat ko'proq — Ko'p serverlar, monitoring tizimi, CI/CD pipeline — infratuzilma narxi yuqori.
Ishlab chiqish boshlash qiyin — Yangi dasturchi loyihaga kirishishi uchun ko'proq vaqt kerak.



⚖️ Taqqoslash jadvali

Mezon                      | Monolitik        | Mikroservis
---------------------------|------------------|----------------------
Ishlab chiqish tezligi     | ✅ Tez           | ❌ Sekin (boshlang'ichda)
Miqyoslanish               | ❌ Cheklangan    | ✅ Cheksiz
Deploy murakkabligi        | ✅ Oddiy         | ❌ Murakkab
Xatolarga chidamlilik      | ❌ Past          | ✅ Yuqori
Texnologiya erkinligi      | ❌ Yo‘q          | ✅ Bor
Debugging                  | ✅ Oson          | ❌ Qiyin
Infratuzilma narxi         | ✅ Arzon         | ❌ Qimmat
Katta jamoalar uchun       | ❌ Mos emas      | ✅ Ideal
Boshlang‘ich loyiha        | ✅ Ideal         | ❌ Ortiqcha murakkab

🏆 Tavsiya: Qaysi biri yaxshiroq?
➡️ Dastlabki bosqich uchun: Monolitik arxitektura (Variant 1)
Sabablari:

MVP tez chiqariladi — Apartment Rental Platform hali yangi loyiha. Birinchi versiyani bozorga chiqarish uchun monolitik yondashuv 2-3 barobar tezroq.
Jamoa kichik — Boshlang'ich bosqichda 3-5 dasturchi ishlaydi. Mikroservislarni boshqarish uchun DevOps mutaxassisi va katta jamoa kerak bo'ladi.
Foydalanuvchilar soni noaniq — Dastlab tizim katta yuklanmaga duch kelmaydi. Vertikal scaling (server kuchaytiriladi) yetarli.
"Modular Monolith" yondashuvi — Kod dastdan to'g'ri modullar bilan yozilsa (Auth, Listing, Booking alohida papkalar), keyinchalik mikroservislarga o'tish oson bo'ladi.

📈 O'sish bosqichi uchun: Mikroservislarga o'tish
Quyidagi holatlar yuzaga kelganda mikroservislarga o'tish tavsiya etiladi:

Kunlik faol foydalanuvchilar 10,000+ ga yetganda
Jamoada 8+ dasturchi ishlayotganda
Bitta modul (masalan, Listing) boshqalardan 5x ko'proq yuklanayotganda
Tizim uptime talabi 99.99% ga ko'tarilganda


Xulosa: "Start monolith, evolve to microservices" — dunyodagi ko'plab yirik kompaniyalar (Netflix, Uber, Airbnb) ham aynan shu yo'ldan o'tgan. Monolitdan boshlang, o'sish bilan mikroservislarga asta-sekin ko'ching.


