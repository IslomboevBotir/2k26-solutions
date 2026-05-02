# Tourist Attraction Review Site 

## 1-bosqich: Talablar (Requirements)

### Funktsional talablar

1. Foydalanuvchi tizimdan ro‘yxatdan o‘tishi va tizimga kirishi mumkin bo‘lishi kerak.
2. Foydalanuvchi turistik joylar haqida sharh (review) qoldira olishi kerak.
3. Foydalanuvchi turistik joylarga reyting (1 dan 5 gacha) berishi mumkin bo‘lishi kerak.
4. Foydalanuvchi turistik joylarni qidirish (nomi yoki joylashuvi bo‘yicha) imkoniyatiga ega bo‘lishi kerak.
5. Foydalanuvchi boshqa foydalanuvchilarning sharhlarini ko‘rishi mumkin bo‘lishi kerak.

---

### Nofunktsional talablar

1. Tizim bir vaqtning o‘zida kamida 1000 ta foydalanuvchini qo‘llab-quvvatlashi kerak.
2. Tizim javob berish vaqti 2 soniyadan oshmasligi kerak.
3. Tizim 24/7 ishlashi (uzluksiz ishlash) talab qilinadi.
4. Foydalanuvchi ma’lumotlari xavfsizligi ta’minlanishi kerak (parollarni shifrlash, autentifikatsiya).
5. Interfeys mobil qurilmalar uchun moslashuvchan (responsive design) bo‘lishi kerak.

---

## 2-bosqich: Arxitektura

### Variant 1: Monolit arxitektura

#### Tizim sxemasi

Frontend (Web/Mobile)
        ↓
Backend (bitta yagona server)
        ↓
Database (Ma’lumotlar bazasi)

#### Afzalliklari

- Oddiy va tez ishlab chiqiladi
- Loyihani boshlash oson
- Kichik loyihalar uchun qulay

#### Kamchiliklari

- Masshtablash qiyin
- Tizim kattalashganda ishlash samaradorligi pasayadi
- Bitta xatolik butun tizimni to‘xtatishi mumkin

---

### Variant 2: Mikroxizmatlar (Microservices) arxitekturasi

#### Tizim sxemasi

Frontend
   ↓
API Gateway
   ↓
----------------------------------
| User Service                  |
| Review Service                |
| Search Service                |
----------------------------------
   ↓
Har bir servis uchun alohida database

#### Afzalliklari

- Oson masshtablash (scale qilish mumkin)
- Har bir modul mustaqil ishlaydi
- Yangi funksiyalarni qo‘shish oson
- Katta loyihalar uchun mos

#### Kamchiliklari

- Murakkab tuzilma
- Ko‘proq server va resurs talab qiladi
- Boshqarish va monitoring qiyinroq

---

## Qaysi arxitektura yaxshiroq?

Mikroxizmatlar arxitekturasi yaxshiroq hisoblanadi.

### Sabablari:

- Tizim kelajakda kengayishi mumkin
- Har bir xizmatni alohida rivojlantirish mumkin
- Yuqori yuklama (traffic) bo‘lganda tizim barqaror ishlaydi
- Zamonaviy loyihalar uchun standart yechim

---


