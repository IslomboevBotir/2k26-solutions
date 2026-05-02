# 🏗️ Construction Project Tracker
 
## 1-bosqich: Talablar
 
### ✅ Funksional talablar (Functional Requirements)
 
1. **Loyihalarni boshqarish**
   - Yangi loyiha qo'shish, tahrirlash va o'chirish imkoniyati.
2. **Vazifalar va bosqichlarni kuzatish**
   - Har bir vazifaning holati (bajarilmagan, jarayonda, bajarilgan) va muddatini real vaqt rejimida ko'rish.
3. **Resurslarni boshqarish**
   - Ishchi kuchi, materiallar va texnikani loyihaga biriktirish va ularning sarfini kuzatish.
4. **Byudjet nazorati**
   - Rejalashtirilgan va haqiqiy xarajatlarni taqqoslash, ortiqcha sarflanishni ogohlantirish.
5. **Hisobotlar yaratish**
   - Haftalik, oylik va loyiha yakunidagi progress hisobotlarini generatsiya qilish.
6. **Foydalanuvchi autentifikatsiyasi**
   - Menejer, muhandis va ishchi uchun role-based access tizimi.
---
 
### ⚙️ Nofunksional talablar (Non-Functional Requirements)
 
1. **Tezkorlik (Performance)**
   - Tizim 1 soniya ichida javob qaytarishi kerak.
2. **Xavfsizlik (Security)**
   - Parollar hash qilinishi va JWT orqali autentifikatsiya ishlatilishi kerak.
3. **Masshtablanuvchanlik (Scalability)**
   - Bir vaqtda bir nechta loyihani parallel boshqarish imkoniyati bo'lishi kerak.
4. **Ishonchlilik (Reliability)**
   - Tizim 99% uptime bilan ishlashi va ma'lumotlar yo'qolmasligi zarur.
5. **Foydalanish qulayligi (Usability)**
   - Qurilish sohasidagi mutaxassislar uchun oddiy va tushunarli UI/UX bo'lishi kerak.
---
 
## 2-bosqich: Arxitektura
 
### 🏗️ Variant 1: Monolit Arxitektura
 
#### 📊 Tizim sxemasi
 
      👤 User (Manager / Engineer / Worker)
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
    │ • Project Module        │
    │ • Task Module           │
    │ • Resource Module       │
    │ • Budget Module         │
    │ • Reports Module        │
    └──────────┬──────────────┘
               │ SQL Queries
               ▼
    ┌─────────────────────────┐
    │           DB            │
    │ • Projects              │
    │ • Tasks                 │
    │ • Resources             │
    │ • Budget                │
    │ • Users                 │
    └─────────────────────────┘
 
#### 👍 Afzalliklari
- Oddiy va tez ishlab chiqiladi
- Deployment qilish oson
- Kichik va o'rta loyihalar uchun ideal
- Debug qilish oson
#### 👎 Kamchiliklari
- Loyihalar soni ko'paysa murakkablashadi
- Har bir o'zgarish butun tizimga ta'sir qiladi
- Masshtablash qiyin
---
 
### 🏗️ Variant 2: Mikroservis Arxitekturasi
 
#### 📊 Tizim sxemasi
 
        👤 User (Manager / Engineer / Worker)
                    │
                    ▼
        ┌─────────────────────────┐
        │     Frontend (UI)       │
        └──────────┬──────────────┘
                   │ HTTP Request
                   ▼
        ┌─────────────────────────┐
        │       API Gateway       │
        └──────────┬──────────────┘
                   │ Routes to Services
                   ▼
    ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐
    │  Auth Service   │  │ Project Service │  │Resource Service │  │ Budget Service  │
    │ • User Mgmt     │  │ • Tasks         │  │ • Workers       │  │ • Expenses      │
    │ • Roles         │  │ • Milestones    │  │ • Materials     │  │ • Reports       │
    └───────┬─────────┘  └───────┬─────────┘  └───────┬─────────┘  └───────┬─────────┘
            │ SQL                │ SQL                 │ SQL                │ SQL
            ▼                    ▼                     ▼                    ▼
    ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐
    │   DB (Auth)     │  │  DB (Projects)  │  │ DB (Resources)  │  │   DB (Budget)   │
    └─────────────────┘  └─────────────────┘  └─────────────────┘  └─────────────────┘
 
#### 👍 Afzalliklari
- Har bir servis mustaqil ishlaydi
- Oson masshtablash mumkin
- Katta qurilish kompaniyalari uchun mos
- Texnologiyalarni alohida tanlash mumkin
#### 👎 Kamchiliklari
- Murakkab implementatsiya
- Deployment qiyinroq
- Servislar orasida kommunikatsiya kerak
- Ko'proq resurs talab qiladi
---
 
## 🏆 Qaysi biri yaxshiroq?
 
### ✅ Monolit Arxitektura
 
### 💡 Sababi:
- Loyiha kichik yoki o'rta qurilish kompaniyasi uchun mo'ljallangan
- Tez va arzon ishlab chiqish muhim
- Mikroservislar bu bosqichda ortiqcha murakkablik keltiradi
- Kelajakda zarur bo'lsa mikroservisga o'tish mumkin
