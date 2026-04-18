
# Laundry Pickup Service

Mavzu: Tizim talablari va backend arxitekturasi  -- Laundry Pickup Service 

---

## Loyiha haqida

Ushbu loyiha kir yuvish xizmatlari uchun buyurtmalarni boshqarish tizimini yaratishga qaratilgan. Foydalanuvchi uyidan chiqmasdan kir yuvish xizmatiga buyurtma berishi, xizmat vaqtini belgilashi va buyurtma holatini kuzatishi mumkin.

---

## Maqsad

* Kir yuvish xizmatini onlayn boshqarish
* Buyurtmalarni qabul qilish va kuzatish
* Foydalanuvchi va xizmat ko‘rsatuvchi o‘rtasidagi jarayonni soddalashtirish

---

# 1. Tizim talablari

## 1.1 Funksional talablar

1. Foydalanuvchi ro‘yxatdan o‘tishi va tizimga kirishi kerak
2. Foydalanuvchi kir yuvish uchun buyurtma berishi mumkin
3. Foydalanuvchi pickup vaqtini tanlashi kerak
4. Tizim buyurtma holatini ko‘rsatishi kerak (pending, in progress, done)
5. Foydalanuvchi o‘z buyurtmalar tarixini ko‘rishi mumkin

---

## 1.2 Nofunksional talablar

1. Tizim tez ishlashi kerak (API javob vaqti < 1 soniya)
2. Tizim xavfsiz bo‘lishi kerak (authentication va authorization)
3. Tizim bir nechta foydalanuvchilar bilan bir vaqtda ishlay olishi kerak
4. Interfeys oddiy va tushunarli bo‘lishi kerak
5. Ma’lumotlar ishonchli saqlanishi kerak

---

# 2. Backend arxitekturasi

Ushbu loyiha uchun backend arxitekturasi sifatida quyidagi ikki variant ko‘rib chiqildi:

* MVC (Model–View–Controller)
* Clean Architecture

---

## 2.1 MVC (Model–View–Controller)

### Sxema

```id="mvc-laundry"
Client → Controller → Model → Response
```
+--------------------------------------------------+
|                CLIENT (Browser)                  |
|               Angular SPA                        |
+--------------------------------------------------+
                      |
                    HTTPS
                      v
+--------------------------------------------------+
|                BACKEND (.NET MVC)                |
|                                                  |
|  +------------+   +------------+   +-----------+ |
|  | Controller |-->|   Model    |-->| Response  | |
|  +------------+   +------------+   +-----------+ |
|                                                  |
|  (Business logic ko‘pincha Controller ichida)     |
+--------------------------------------------------+
                      |
                      v
+--------------------------------------------------+
|                 DATABASE                         |
|               (SQL Server)                       |
+--------------------------------------------------+
### Tavsif

MVC arxitekturasi tizimni uch qismga ajratadi:

* Model — ma’lumotlar (User, Order, Pickup)
* Controller — so‘rovlarni qabul qiladi va qayta ishlaydi
* View — foydalanuvchiga natijani qaytaradi (API response)

### Afzalliklari

* Oddiy va tez ishlab chiqiladi
* Tushunish oson
* Kichik loyihalar uchun mos

### Kamchiliklari

* Business logic controller ichida aralashib ketadi
* Katta tizimlarda kodni boshqarish qiyinlashadi
* Testlash murakkablashadi

---

## 2.2 Clean Architecture

### Sxema


+--------------------------------------------------+
|                CLIENT (Browser)                  |
|               Angular SPA                        |
+--------------------------------------------------+
                      |
                    HTTPS
                      v
+--------------------------------------------------+
|                API LAYER (.NET)                  |
|                Controllers                       |
+--------------------------------------------------+
                      |
                      v
+--------------------------------------------------+
|            APPLICATION LAYER                     |
|         Services / Use Cases                     |
+--------------------------------------------------+
                      |
                      v
+--------------------------------------------------+
|               DOMAIN LAYER                       |
|        Entities (User, Order, Item)              |
+--------------------------------------------------+
                      |
                      v
+--------------------------------------------------+
|           INFRASTRUCTURE LAYER                   |
|      Repository / DB / External Services         |
+--------------------------------------------------+
                      |
                      v
+--------------------------------------------------+
|                 DATABASE                         |
|               (SQL Server)                       |
+--------------------------------------------------+
### Tavsif

Clean Architecture tizimni qatlamlarga ajratadi va biznes logikani alohida saqlaydi.

* Domain — asosiy entitylar (User, Order, Pickup)
* Application — biznes logika
* Infrastructure — database bilan ishlash
* API — tashqi so‘rovlarni qabul qiladi

### Afzalliklari

* Kod tartibli va tushunarli
* Business logic ajratilgan
* Testlash oson
* Kengaytirish qulay
* Katta loyihalar uchun mos

### Kamchiliklari

* Boshlanishida murakkabroq
* Ko‘proq vaqt talab qiladi

---

# 3. Taqqoslash

| Mezоn               | MVC      | Clean Architecture |
| ------------------- | -------- | ------------------ |
| Oddiylik            | Yuqori   | O‘rtacha           |
| Kengaytirish        | Past     | Yuqori             |
| Testlash            | Qiyinroq | Oson               |
| Professional daraja | O‘rtacha | Yuqori             |

---

# 4. Tanlov

Ushbu loyiha uchun Clean Architecture tanlandi.

Sabablari:

* Business logic alohida va tartibli bo‘ladi
* Tizimni kengaytirish oson
* Kodni test qilish qulay
* Zamonaviy yondashuv

---

# 5. Backend struktura

```id="laundry-structure"
Laundry.API
Laundry.Application
Laundry.Domain
Laundry.Infrastructure
```

---

# 6. So‘rov oqimi

```id="laundry-flow"
Controller → Application → Domain → Infrastructure → Database
```

---

# Xulosa

MVC oddiy va tezkor yechim bo‘lsa-da, ushbu loyiha uchun Clean Architecture tanlandi. Bu yondashuv tizimni tartibli, kengaytiriladigan va kelajakda rivojlantirish uchun qulay qiladi.

