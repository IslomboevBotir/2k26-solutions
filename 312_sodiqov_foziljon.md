# Antique Auction Platform

## 1-bosqich: Tizim talablari

### 1.1. Funksional talablar (5 ta)

1. **Ro'yxatdan o'tish va autentifikatsiya**
   - Foydalanuvchi email/telefon orqali ro'yxatdan o'tishi, tizimga kirishi va parolini tiklashi kerak.

2. **Lot (antik buyum) joylash**
   - Sotuvchi lot yaratishi, rasmlar yuklashi, tavsif, boshlang'ich narx, minimal qadam va auktsion tugash vaqtini belgilashi kerak.

3. **Real vaqt rejimida stavka qo'yish**
   - Xaridor lot bo'yicha stavka qo'yishi, joriy eng yuqori stavkani ko'rishi va stavkasi oshib ketganda bildirishnoma olishi kerak.

4. **Auktsionni avtomatik yopish**
   - Belgilangan vaqt tugagach tizim auktsionni yopishi, g'olibni aniqlashi va natijani saqlashi kerak.

5. **G'olibni tasdiqlash va aloqa ma'lumotlarini ulashish**
   - Auktsion tugagach g'olib foydalanuvchi lotni tasdiqlashi, tizim esa sotuvchi va g'olibga o'zaro bog'lanish uchun aloqa ma'lumotlarini yuborishi kerak.

### 1.2. Nofunksional talablar (5 ta)

1. **Ishlash tezligi**
   - Lot ro'yxatini ochish va qidiruv natijalari 2 soniyadan oshmagan vaqtda chiqishi kerak.

2. **Masshtablilik**
   - Tizim kamida 10 000 bir vaqtning o'zida faol foydalanuvchini qo'llab-quvvatlashi kerak.

3. **Xavfsizlik**
   - Parollar xeshlangan holda saqlanishi, transport qatlamida TLS ishlatilishi va role-based access control qo'llanishi kerak.

4. **Mavjudlik (availability)**
   - Tizimning yillik ishlash ko'rsatkichi kamida 99% bo'lishi kerak.

5. **Audit va kuzatuvchanlik**
   - Stavkalar, g'oliblik natijalari va lot holati bo'yicha barcha muhim voqealar loglanishi va keyin audit uchun olinishi kerak.

---

## 2-bosqich: Tizim arxitekturasi

### Variant A: Monolit arxitektura

#### Tizim sxemasi

```text
Web Layer:
[Web Client]
     |
  HTTPS
     |
Application Layer:
[Monolith App]
  |- Auth Module
  |- Lots Module
  |- Bidding Module
  |- Winner Confirmation Module
  `- Notifications Module
     |
Data Layer:
[MongoDB] + [Object Storage]
```

#### Afzalliklari
- Ishga tushirish tez va sodda.
- Bitta kod bazani boshqarish oson.
- Kichik jamoa uchun rivojlantirish xarajati kamroq.
- Lokal test va debug qilish nisbatan yengil.

#### Kamchiliklari
- Tizim kattalashganda deploy qilish xavfliroq (hammasi birga chiqadi).
- Bidding kabi yuklama yuqori modulni alohida masshtablash qiyin.
- Kod bazasi o'sishi bilan qo'llab-quvvatlash murakkablashadi.

---

### Variant B: Mikroservis arxitektura

#### Tizim sxemasi

```text
[Web Client]
     |
[API Gateway]
  |      |         |                    |
[Auth] [Lot]   [Bidding]   [Winner-Confirmation]
                  |
      [Notification Service]

Infratuzilma:
- Har bir servis: MongoDB/Redis
- Servislar aro xabar almashinuvi: RabbitMQ/Kafka
- Rasmlar: Object Storage
```

#### Afzalliklari
- Har bir servisni alohida masshtablash mumkin (masalan, Bidding servisini).
- Mustaqil deploy: bitta servis yangilansa boshqalarga ta'siri kam.
- Xatolik izolyatsiyasi yaxshiroq.

#### Kamchiliklari
- Boshlang'ich sozlash va infratuzilma murakkabroq.
- Monitoring, tracing va observability uchun qo'shimcha vositalar kerak.
- Tarmoqli chaqiriqlar sabab kechikish va distributed transaction muammolari bo'lishi mumkin.

---

## Qaysi biri yaxshiroq?

`Antique auction platform` uchun hozirgi holatda **Variant A (monolit)** yaxshiroq:

- Dastur hajmi kichik bo'lgani uchun bitta ilova ichida boshqarish osonroq.
- Ishga tushirish, test va deploy jarayoni tezroq bo'ladi.
- Infratuzilma xarajati pastroq bo'ladi.
- Murakkab mikroservis sozlamalarisiz asosiy funksiyalarni tezroq topshirish mumkin.

Loyiha kattalashib, yuklama sezilarli oshsa keyinchalik mikroservis arxitekturaga o'tish mumkin.
