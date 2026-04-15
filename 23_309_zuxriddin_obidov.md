# 🧾 Inventory System for Small Shop

## 1-bosqich: Talablar

### ✅ Funksional talablar (Functional Requirements)

1. **Mahsulotlarni boshqarish**
   - Yangi mahsulot qo‘shish, tahrirlash va o‘chirish imkoniyati.

2. **Ombordagi zaxiralarni kuzatish**
   - Har bir mahsulotning miqdorini real vaqt rejimida ko‘rish.

3. **Sotuvlarni qayd etish**
   - Har bir sotuv operatsiyasini tizimga kiritish va avtomatik ravishda zaxiradan ayirish.

4. **Hisobotlar yaratish**
   - Kunlik, haftalik va oylik savdo hisobotlarini generatsiya qilish.

5. **Foydalanuvchi autentifikatsiyasi**
   - Admin va oddiy foydalanuvchilar uchun login tizimi (role-based access).

---

### ⚙️ Nofunksional talablar (Non-Functional Requirements)

1. **Tezkorlik (Performance)**
   - Tizim 0.5 soniya ichida javob qaytarishi kerak.

2. **Xavfsizlik (Security)**
   - Parollar hash qilinishi va JWT orqali autentifikatsiya ishlatilishi kerak.

3. **Masshtablanuvchanlik (Scalability)**
   - Tizim foydalanuvchilar soni oshganda ham ishlashda davom etishi kerak.

4. **Ishonchlilik (Reliability)**
   - Tizim 99% uptime bilan ishlashi zarur.

5. **Foydalanish qulayligi (Usability)**
   - Oddiy va tushunarli UI/UX bo‘lishi kerak.

---

## 2-bosqich: Arxitektura

### 🏗️ Variant 1: Monolit Arxitektura

#### 📊 Tizim sxemasi

      👤 User (Admin / Staff)
                │
                ▼
    ┌─────────────────────────┐
    │        Frontend         │
    └──────────┬──────────────┘
               │ HTTP Request
               ▼
    ┌─────────────────────────┐
    │         Backend         │
    │ ─────────────────────── │
    │ • Auth Module           │
    │ • Inventory Module      │
    │ • Sales Module          │
    │ • Reports Module        │
    └──────────┬──────────────┘
               │ SQL Queries
               ▼
    ┌─────────────────────────┐
    │         DB              │
    │ • Products              │
    │ • Sales                 │
    │ • Users                 │
    └─────────────────────────┘

#### 👍 Afzalliklari
- Oddiy va tez ishlab chiqiladi
- Deployment qilish oson
- Kichik loyihalar uchun ideal
- Debug qilish oson

#### 👎 Kamchiliklari
- Katta bo‘lsa murakkablashadi
- Har bir o‘zgarish butun tizimga ta’sir qiladi
- Masshtablash qiyin


---

### 🏗️ Variant 2: Mikroservis Arxitekturasi

#### 📊 Tizim sxemasi

        👤 User (Admin / Staff)
                    │
                    ▼
        ┌─────────────────────────┐
        │     Frontend (UI)       │
        └──────────┬──────────────┘
                   │ HTTP Request
                   ▼
        ┌─────────────────────────┐
        │     API Gateway         │
        └──────────┬──────────────┘
                   │ HTTP Request
                   ▼
        ┌─────────────────────────┐   ┌──────────────────────────┐   ┌─────────────────────────┐
        │ Auth Service            │   │ Inventory Service        │   │ Sales Service           │
        │ • User Management       │   │ • Product Management     │   │ • Sales Management      │
        └──────────┬──────────────┘   └──────────┬───────────────┘   └──────────┬──────────────┘
                   │ SQL Queries                 │ SQL Queries                  │ SQL Queries
                   ▼                             ▼                              ▼
        ┌──────────────────────────┐   ┌──────────────────────────┐   ┌─────────────────────────┐
        │         DB (Auth)        │   │      DB (Inventory)      │   │        DB (Sales)       │
        └──────────────────────────┘   └──────────────────────────┘   └─────────────────────────┘

#### 👍 Afzalliklari
- Har bir servis mustaqil ishlaydi
- Oson masshtablash mumkin
- Katta tizimlar uchun mos
- Texnologiyalarni alohida tanlash mumkin

#### 👎 Kamchiliklari
- Murakkab implementatsiya
- Deployment qiyinroq
- Servislar orasida kommunikatsiya kerak
- Ko‘proq resurs talab qiladi

---

## 🏆 Qaysi biri yaxshiroq?

###  Monolit Arxitektura

### 💡 Sababi:
- Loyiha kichik do‘kon uchun mo‘ljallangan
- Tez va arzon ishlab chiqish muhim
- Mikroservislar bu bosqichda ortiqcha murakkablik keltiradi