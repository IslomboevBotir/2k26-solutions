# Online Bakery System Loyihasi

                                                --1-bosqich--

--5 ta funksional talab--

1. Foydalanuvchi boshqaruvi**: Ro‘yxatdan o‘tish, email/OTP tasdiqlash, login, parolni tiklash va shaxsiy profil (manzil, buyurtmalar tarixi) boshqarish.
2. Mahsulot katalogi**: Mahsulotlarni ko‘rish, qidiruv, filtrlar (kategoriya, narx, yangilik), sahifalash.
3. Savatcha va buyurtma**: Savatchaga qo‘shish, miqdor o‘zgartirish, yetkazib berish manzilini tanlash va buyurtma yaratish.
4. To‘lov va bildirishnomalar**: Onlayn to‘lov (Click/Payme), buyurtma holatini real-time yangilash va email/SMS xabarnoma yuborish.
5. Admin paneli**: Mahsulot qo‘shish/tahrirlash/o‘chirish, buyurtmalarni boshqarish, zaxira holatini kuzatish va hisobotlar chiqarish.

--5 ta nofunksional talab--

1. Ishlash samaradorligi**: 500+ bir vaqtda foydalanuvchi so‘rovini 2 soniyadan kam vaqtda qayta ishlash.
2. Xavfsizlik**: JWT autentifikatsiya, HTTPS, SQL injection va XSS himoyasi, ma’lumotlar shifrlash.
3. Masshtablashuvchanlik**: Vertikal va gorizontal masshtablash imkoniyati.
4. Ishonchlilik**: 99.95% uptime, avtomatik backup va recovery.
5. Saqlanuvchanlik**: Modulli kod, yaxshi hujjatlashtirish va test qamrovi.

                                                2-bosqich. Arxitektura

--1-variant: Monolitik arxitektura (Spring Boot Monolith)--

Mijoz (Web/Mobile)
↓ (HTTP/HTTPS)
Spring Boot Monolit Ilova
├── REST Controllers
├── Business Services
├── Spring Security + JWT
├── JPA Repositories
├── Redis Cache
└── Payment Integration
↓
PostgreSQL Database

-Afzalliklari:
- Rivojlantirish va test qilish oson
- Bitta loyiha, bitta deployment
- Tez ishlash (network latency yo‘q)
- Kichik loyihalar uchun ideal

-Kamchiliklari:
- Loyiha kattalashganda kod bazasi murakkablashadi
- Bitta xato butun tizimni to‘xtatishi mumkin
- Masshtablash butun ilovani masshtablaydi

---

--2-variant: Mikroservis arxitekturasi (Spring Cloud)--

Mijoz (Web/Mobile)
↓
API Gateway (Spring Cloud Gateway)
↓
├── User Service          → User Database (PostgreSQL)
├── Product Service       → Product Database
├── Order Service         → Order Database
└── Payment Service       → Payment Database
Qo‘shimcha komponentlar:
• Eureka Service Discovery
• Spring Cloud Config Server
• RabbitMQ (Event-driven aloqa)
• Zipkin (Distributed Tracing)

-Tushuntirish:
- Har bir servis o‘ziga alohida ma’lumotlar bazasiga ega.
- Servislar o‘rtasidagi aloqa API Gateway orqali amalga oshiriladi.
- Buyurtma va to‘lov o‘rtasidagi aloqa RabbitMQ orqali asinxron (event-driven) tarzda bo‘ladi.

-Afzalliklari:
- Har bir servisni alohida masshtablash mumkin
- Turli jamoalar parallel ravishda ishlashi mumkin
- Bitta servisda xato bo‘lsa, boshqa servislar ishlashda davom etadi
- Kelajakda yangi funksiyalarni osongina qo‘shish mumkin

-Kamchiliklari:
- Tizim murakkabroq bo‘ladi (network, tracing, monitoring)
- Deployment va boshqarish qiyinroq (Docker + Kubernetes kerak)
- Distributed transaction muammolari paydo bo‘ladi
- Dastlabki rivojlantirish va o‘rganish vaqti ko‘p

---

                                        --Qaysi biri yaxshiroq va nima uchun?--

                        1-variant (Monolitik arxitektura) bu loyiha uchun yaxshiroq hisoblanadi.

Sababi : 
- Online Bakery System — o‘rta darajadagi loyiha.
- Monolit tezroq ishlab chiqiladi va ishga tushiriladi.
- Mikroservisning qo‘shimcha murakkabligi (distributed systems muammolari) hozircha kerak emas.
- Keyinchalik loyiha katta bo‘lib, yuklama oshganda monolitni bosqichma-bosqich mikroservislarga ajratish mumkin (Strangler Fig pattern).