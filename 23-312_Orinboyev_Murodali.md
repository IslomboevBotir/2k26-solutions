# Digital Recipe Organizer

## 1-bosqich: Tizim talablari

### 📌 Funksional talablar (Functional Requirements)

1. Foydalanuvchi ro‘yxatdan o‘tishi va tizimga kirishi (login/register).
2. Retsept qo‘shish, tahrirlash va o‘chirish imkoniyati.
3. Retseptlarni kategoriya bo‘yicha ajratish (nonushta, tushlik, shirinlik va h.k.).
4. Retseptlarni qidirish (nomi yoki ingredient orqali).
5. Foydalanuvchi o‘z sevimli retseptlarini “favorites”ga qo‘shishi.

---

### 📌 Nofunksional talablar (Non-Functional Requirements)

1. Tizim tez ishlashi (search natijalari 2 soniyadan kam vaqt ichida chiqishi).
2. Mobil va desktop qurilmalarga mos dizayn (responsive UI).
3. Ma’lumotlar xavfsizligi (parollar hash qilinadi).
4. Tizim 99% uptime bilan ishlashi.
5. Kod strukturalangan va kengaytiriladigan bo‘lishi (scalable architecture).

---

## 2-bosqich: Arxitektura

## 🏗 Variant 1: Monolit arxitektura

### 📊 Tizim sxemasi:
                ┌───────────────────────────┐
                │        Foydalanuvchi      │
                │ (Web / Mobile Browser)    │
                └─────────────┬─────────────┘
                              │ HTTP Request
                              ▼
                ┌───────────────────────────┐
                │        Frontend UI        │
                │ (HTML / CSS / JS)         │
                └─────────────┬─────────────┘
                              │ API Request
                              ▼
        ┌────────────────────────────────────────┐
        │          MONOLIT ILOVA (Backend)       │
        │                                        │
        │  ┌──────────────┐  ┌────────────────┐  │
        │  │ Auth Module  │  │ Recipe Module  │  │
        │  │ (Login/Register) │ (CRUD ops)    │  │
        │  ├──────────────┤  ├────────────────┤  │
        │  │ Category     │  │ Favorites      │  │
        │  │ Module       │  │ Module         │  │
        │  └──────────────┘  └────────────────┘  │
        └──────────────────────┬─────────────────┘
                               │ ORM / SQL
                               ▼
                    ┌─────────────────────┐
                    │     Database        │
                    │ (MySQL / PostgreSQL)│
                    └─────────────────────┘
### 👍 Afzalliklari:
- Oson ishlab chiqiladi
- Kichik loyiha uchun ideal
- Deployment sodda
- Debug qilish yengil

### 👎 Kamchiliklari:
- Katta loyihalarda sekinlashadi
- Kengaytirish qiyin
- Barcha modul bir joyda bog‘langan

## 🏗 Variant 2: Client-Server (REST API + Frontend separation) ### 📊 Tizim sxemasi:
        ┌──────────────────────────────┐
        │        Foydalanuvchi         │
        │ (Browser / Mobile App)       │
        └──────────────┬───────────────┘
                       │
                       ▼
        ┌──────────────────────────────┐
        │   Frontend (React / Vue)     │
        │  UI + State Management       │
        └──────────────┬───────────────┘
                       │  REST API Request (JSON)
                       ▼
        ┌────────────────────────────────────┐
        │   Backend (Django REST Framework)  │
        │                                    │
        │  ┌──────────────┐  ┌─────────────┐ │
        │  │ Auth System  │  │ Recipe API  │ │
        │  │ (JWT/Login)  │  │ CRUD ops    │ │
        │  ├──────────────┤  ├─────────────┤ │
        │  │ Category API │  │ Favorites   │ │
        │  └──────────────┘  └─────────────┘ │
        └──────────────┬─────────────────────┘
                       │ ORM / SQL Queries
                       ▼
        ┌──────────────────────────────┐
        │       Database               │
        │   PostgreSQL / MySQL         │
        └──────────────────────────────┘

### 👍 Afzalliklari:
- Frontend va backend ajratilgan
- Kengaytirish oson
- Mobile app qo‘shish mumkin
- Zamonaviy architecture

### 👎 Kamchiliklari:
- Murakkabroq setup
- Ko‘proq vaqt talab qiladi
- API dizayn qilish kerak

---

## ⚖ Qaysi biri yaxshiroq?

👉 **Client-Server (REST API architecture)** yaxshiroq.

### Nima uchun:
- Kelajakda mobil ilova qo‘shish mumkin
- Kod modular va scalable
- Real loyihalarga yaqin professional yondashuv
- Frontend va backend mustaqil ishlaydi

---

## 📌 Xulosa

Digital Recipe Organizer kichik loyiha bo‘lsa ham, uni REST API arxitekturada qilish uni real-world professional tizimga yaqinlashtiradi va keyinchalik kengaytirishni oson qiladi.

