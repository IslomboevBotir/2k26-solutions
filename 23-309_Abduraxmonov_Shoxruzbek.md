# Fitness Trainer Booking System — Tizim Talablari va Arxitekturasi

**Guruh:** 23-309  
**Talaba:** Abduraxmonov Shoxruzbek  
**Loyiha mavzusi:** Fitness Trainer Booking System

---

## 1-bosqich. Talablar

### Funksional talablar

| # | Talab |
|---|-------|
| F1 | Foydalanuvchi ro'yxatdan o'tishi va tizimga kirishi (mijoz va trener rollari bilan) |
| F2 | Trener profili: mutaxassislik, narx, band/bo'sh vaqtlar, reyting |
| F3 | Mijoz trener qidirishi, filtr qo'llashi va mashg'ulotga yozilishi |
| F4 | Bron tasdiqlash, bekor qilish va qayta rejalashtirish |
| F5 | Foydalanuvchilar o'rtasida chat va bildirishnomalar (SMS/email/push) |

### Nofunksional talablar

| # | Talab |
|---|-------|
| NF1 | **Ishlash tezligi:** Sahifa yuklash vaqti ≤ 2 soniya, API javob vaqti ≤ 500ms |
| NF2 | **Xavfsizlik:** JWT autentifikatsiya, HTTPS, parollar bcrypt bilan shifrlangan |
| NF3 | **Kengayuvchanlik:** Bir vaqtda ≥ 10,000 foydalanuvchini qo'llab-quvvatlash |
| NF4 | **Mavjudlik:** Uptime ≥ 99.5%, kunlik zaxira nusxa (backup) |
| NF5 | **Foydalanish qulayligi:** Mobil qurilmaga moslashgan (responsive) UI, UX testlardan o'tgan |

---

## 2-bosqich. Arxitektura variantlari

---

### Variant 1 — Monolitik arxitektura (MVC)

```
┌─────────────────────────────────────────────────────────┐
│                    CLIENT (Browser/Mobile)               │
└───────────────────────┬─────────────────────────────────┘
                        │ HTTP/HTTPS
┌───────────────────────▼─────────────────────────────────┐
│              MONOLITHIC APPLICATION SERVER               │
│  ┌────────────┐  ┌───────────────┐  ┌────────────────┐  │
│  │  Auth      │  │   Booking     │  │  Notification  │  │
│  │  Module    │  │   Module      │  │  Module        │  │
│  └────────────┘  └───────────────┘  └────────────────┘  │
│  ┌────────────┐  ┌───────────────┐  ┌────────────────┐  │
│  │  Trainer   │  │   Schedule    │  │   Payment      │  │
│  │  Module    │  │   Module      │  │   Module       │  │
│  └────────────┘  └───────────────┘  └────────────────┘  │
└───────────────────────┬─────────────────────────────────┘
                        │
┌───────────────────────▼──────────────┐
│         PostgreSQL Database          │
│  (Users, Trainers, Bookings,         │
│   Schedules, Payments, Messages)     │
└──────────────────────────────────────┘
```

**Afzalliklari:**
- Ishlab chiqish tezligi yuqori — bitta kod bazasi
- Deploy qilish oson — bitta server
- Ichki chaqiriqlar tez (network yo'q)
- Debugging va monitoring oson
- Kichik jamoa uchun qulay

**Kamchiliklari:**
- Kengaytirish qiyin — butun tizimni scale qilish kerak
- Bir modul xatosi butun tizimni to'xtatishi mumkin
- Texnologiyalarni almashtirish murakkab
- Katta loyihada kod bazasi murakkablashib ketadi
- CI/CD jarayoni sekin (har deploy da hammasi qayta yuklanadi)

---
