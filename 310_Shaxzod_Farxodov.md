# Mavzu: Online Flower Delivery System (Gullar yetkazib berish tizimi)

## 1-bosqich. Talablar

### Funksional talablar
| #  | Talab                                                                                |
| -- | ------------------------------------------------------------------------------------ |
| F1 | Foydalanuvchi ro'yxatdan o'tishi va tizimga kirishi (Mijoz, Admin, Kurer rollari)    |
| F2 | Katalog ko‘rish: Gullarni toifa, narx va bayramlar bo‘yicha saralash va qidirish      |
| F3 | Buyurtma berish: Savat shakllantirish, yetkazib berish manzili va vaqtini kiritish   |
| F4 | Onlayn to‘lov: Click, Payme va bank kartalari orqali to‘lovlarni amalga oshirish    |
| F5 | Buyurtma holatini kuzatish: Buyurtma holatini real vaqtda kuzatish                   |
| F6 | Bildirishnomalar: SMS yoki Push-xabar orqali kurer kelishi haqida ogohlantirish     |
| F7 | Sharh va reyting: Yetkazib berilgan mahsulot va xizmat sifatiga baho berish          |

### Nofunksional talablar
| #   | Talab                                                                               |
| --- | ----------------------------------------------------------------------------------- |
| NF1 | Ishlash tezligi: Sahifa yuklash ≤ 2 soniya, buyurtma tasdiqlanishi ≤ 3 soniya       |
| NF2 | Xavfsizlik: JWT autentifikatsiya, HTTPS, parollar bcrypt bilan shifrlangan          |
| NF3 | Kengayuvchanlik: Bayram kunlari bir vaqtning o‘zida ≥ 10,000 foydalanuvchini qo‘llash|
| NF4 | Mavjudlik: Uptime ≥ 99.9%, ma’lumotlar uchun avtomatik backup                       |
| NF5 | Foydalanish qulayligi: Mobil va kompyuter brauzerlari uchun responsive dizayn       |
| NF6 | Ma'lumotlar butunligi: Har kuni bazani avtomatik zaxiralash (backup)                |

---

## 2-bosqich. Arxitektura variantlari

### Variant 1 — Monolitik arxitektura (MVC)
Tizimning barcha funksiyalari yagona server va yagona kod bazasida joylashgan.

**Tizim sxemasi:**
```text
┌─────────────────────────────────────────────────────────┐
│                 CLIENT (Web/Mobile App)                 │
└───────────────────────┬─────────────────────────────────┘
                        │ HTTP/HTTPS
┌───────────────────────▼─────────────────────────────────┐
│              MONOLITHIC APPLICATION SERVER              │
│                                                         │
│  ┌────────────┐  ┌───────────────┐  ┌────────────────┐  │
│  │    Auth    │  │    Catalog    │  │     Order      │  │
│  │   Module   │  │    Module     │  │     Module     │  │
│  └────────────┘  └───────────────┘  └────────────────┘  │
│                                                         │
│  ┌────────────┐  ┌───────────────┐  ┌────────────────┐  │
│  │   Payment  │  │    Tracking   │  │   Analytics    │  │
│  │   Module   │  │    Module     │  │     Module     │  │
│  └────────────┘  └───────────────┘  └────────────────┘  │
└───────────────────────┬─────────────────────────────────┘
                        │
┌───────────────────────▼────────────────────────────────┐
│                PostgreSQL / MySQL Database             │
│   (Users, Flowers, Orders, Payments, Reviews, Logs)    │
└────────────────────────────────────────────────────────┘
