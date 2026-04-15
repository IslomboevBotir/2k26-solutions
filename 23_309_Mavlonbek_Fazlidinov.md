# Smart Library Seat Reservation — Tizim Talablari va Arxitektura

---

## 1-bosqich: Talablar

### Mavzu: Smart Library Seat Reservation
Aqlli kutubxona uchun joy (o'rindiq) bron qilish tizimi. Foydalanuvchilar mobil yoki veb ilova orqali kutubxona zalida bo'sh joy topib, oldindan band qila oladilar. Tizim real vaqt rejimida joy holatini ko'rsatib turadi.

---

### Funksional talablar

| # | Talab |
|---|-------|
| F1 | Foydalanuvchi ro'yxatdan o'tishi va tizimga kirishi kerak (email/parol yoki Google orqali). |
| F2 | Foydalanuvchi kutubxona zalining interaktiv xaritasida bo'sh joylarni ko'rishi va tanlashi kerak. |
| F3 | Foydalanuvchi ma'lum sana va vaqt oralig'iga joy bron qila olishi kerak. |
| F4 | Tizim bron qilingan joyni tasdiqlash uchun foydalanuvchiga SMS yoki email xabar yuborishi kerak. |
| F5 | Foydalanuvchi o'zining aktiv bronlarini ko'rishi, o'zgartirishi yoki bekor qilishi mumkin bo'lishi kerak. |

---

### Funksional bo'lmagan talablar

| # | Talab |
|---|-------|
| NF1 | **Ishlash tezligi**: Bron so'rovi 2 soniyadan ko'p vaqt olmasligi kerak (95% holatda). |
| NF2 | **Xavfsizlik**: Foydalanuvchi ma'lumotlari shifrlangan holda saqlanishi va JWT autentifikatsiyasi qo'llanilishi kerak. |
| NF3 | **Mavjudlik (Availability)**: Tizim 99.5% uptime kafolati bilan ishlashi kerak (yiliga 43 soatdan ko'p to'xtamaslik). |
| NF4 | **Miqyoslanish**: Tizim bir vaqtda kamida 500 faol foydalanuvchini qo'llab-quvvatlashi kerak. |
| NF5 | **Foydalanuvchanlik**: Interfeys mobil qurilmalar uchun moslashtirilgan (responsive) bo'lishi va ko'r-ko'rona navigatsiyani qo'llab-quvvatlashi kerak. |

---

## 2-bosqich: Arxitektura

---

### Variant 1 — Monolitik Arxitektura

#### Tizim diagrammasi

```
+-------------------+          +--------------------------------------------+          +-------------+
|  Foydalanuvchi    |  HTTP    |              Monolitik ilova                |  SQL     |  PostgreSQL |
|  (Web / Mobil)    | -------> | [Auth] [Bron] [Xarita] [Bildirishnoma]     | -------> |  (bitta DB) |
+-------------------+          | [Admin panel] [Hisobot]                    |          +-------------+
                               +--------------------------------------------+
```

Barcha modullar bitta ilovada joylashgan: Auth, Bron, Xarita, Bildirishnoma, Admin va Hisobot. Ular bitta PostgreSQL ma'lumotlar bazasiga murojaat qiladi.

#### Afzalliklari
- **Soddalik**: Bitta loyiha, bitta server, bitta bazaga ulanish — tushunish va deploy qilish oson.
- **Rivojlantirish tezligi**: Boshlang'ich bosqichda tez ishlab chiqish mumkin, modullar orasida API kerak emas.
- **Debugging**: Barcha jarayon bitta joyda kuzatiladi, xatolarni topish osonroq.
- **Xarajat**: Server va infratuzilma xarajatlari minimum darajada.

#### Kamchiliklari
- **Miqyoslanish qiyinligi**: Faqat bitta komponent yuklansa ham, butun ilovani kengaytirish kerak bo'ladi.
- **Bir nuqtali muvaffaqiyatsizlik**: Ilovaning biror qismi ishlamay qolsa, butun tizim to'xtaydi.
- **Katta kod bazasi**: Vaqt o'tishi bilan kod murakkablashib, yangi funksiya qo'shish qiyinlashadi.
- **Texnologiya bog'liqligi**: Barcha modullar bitta dasturlash tili va framework bilan yozilishi shart.

---

### Variant 2 — Mikroservis Arxitektura

#### Tizim diagrammasi

```
         [Client: Web / Mobil]
                  |
           [API Gateway]
          /    |     |    \       \
   [Auth] [Bron] [Joy] [Xabar] [Hisobot]
    servis servis servis servis  servis
      |      |      |      |       |
   UserDB BronDB SeatDB NotifDB ReportDB
              \     |     /
           [Message Broker: RabbitMQ]
```

Har bir servis mustaqil ishlab chiqiladi, deploy qilinadi va o'z ma'lumotlar bazasiga ega.

#### Afzalliklari
- **Mustaqil miqyoslanish**: Faqat yuklangan servisni (masalan, Bron servisini) alohida kengaytirish mumkin.
- **Yuqori chidamlilik**: Bir servis ishlamay qolsa, qolganlar ishlashda davom etadi.
- **Texnologik erkinlik**: Har bir servis uchun mos til va texnologiya tanlanishi mumkin (Node.js, Python, Go...).
- **Kichik va tushunarli kod**: Har bir servisni alohida jamoa boshqaradi, kod bazasi kichik qoladi.

#### Kamchiliklari
- **Murakkablik**: Servislararo muloqot (API chaqiruvlar, message broker) qo'shimcha infratuzilma talab qiladi.
- **Distributed tracing**: Xatoni bir nechta servis bo'ylab kuzatish qiyin.
- **Katta xarajat**: Ko'plab server, konteyner va monitoring vositalari talab etiladi.
- **Dastlabki sozlash**: Docker, Kubernetes, API Gateway kabi texnologiyalarni o'rnatish va sozlash vaqt talab qiladi.

---

## Qaysi variant yaxshiroq va nima uchun?

**Tavsiya: Monolitik arxitekturadan boshlash (Variant 1)**

Smart Library Seat Reservation loyihasi — o'quv loyihasi bo'lib, real foydalanuvchilar soni birinchi bosqichda cheklangan bo'ladi. Shuning uchun quyidagi sabablarga ko'ra monolitik arxitektura maqsadga muvofiq:

1. Tezroq ishlab chiqish va deploy qilish imkoniyati.
2. Birinchi versiyani kamroq resurs bilan yakunlash mumkin.
3. Talabalar uchun tushunish va debugging ancha oson.
4. Foydalanuvchilar soni va trafik oshib borgach, arxitekturani mikroservisga o'tkazish mumkin.

Kelajakda yuklama oshganda va jamoa kengayganda **Variant 2 (Mikroservis)** ga o'tish tavsiya etiladi.

---

