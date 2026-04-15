Loyiha: Personal Habit Tracker

Muallif: Diyorbek Aliyev (guruh: guruh)  
Sana: 2026-04-15

1. Loyiha mavzusi
Personal Habit Tracker — foydalanuvchilarga odatlarni qo'shish, kuzatish va yutuqlarini ko‘rish imkonini beruvchi veb-ilova (mobil-first).

2. Maqsad
Foydalanuvchilarga kundalik odatlarni tashkil qilish, barqarorlikni o‘lchash va motivatsiyani oshirishga yordam beruvchi yengil va intuitiv tizim yaratish.

3. 1-bosqich — Talablar

5 ta funksional talab
1. Foydalanuvchi ro‘yxatdan o‘tishi va tizimga kirishi (email/username + parol, yoki OAuth).
2. Foydalanuvchi yangi odat (habit) qo‘shishi — nom, tavsif, takrorlanish va maqsad (kunlik/haftalik).
3. Odat bo‘yicha kundalik/haftalik kirishlarni belgilash (checkbox/toggle) va tarix bo‘yicha ko‘rish.
4. Statistik sahifa — haftalik progress, ketma-ket kunlar (streak), va yakuniy oylik hisobot.
5. Odatlarni tahrirlash/ochirish va bildirishnomalar (push/email) sozlamalari.

5 ta nofunksional talab
1. Ishlash tezligi: asosiy sahifa yuklanishi <= 2s oddiy 4G sharoitida.
2. Xavfsizlik: parollar xavfsiz saqlanishi (bcrypt/argon2), HTTPS orqali aloqalar.
3. Ishonchlilik: bazaning muntazam zaxira nusxalari (daily backup).
4. Kengaytiriluvchanlik: foydalanuvchilar soni 10k darajasigacha oddiy o‘zgartirishlarsiz qo‘llab-quvvatlanishi.
5. Foydalanuvchi interfeysi: mobil-first va oddiy, accessibility asosiy talab (keyboard nav, alt matn).

4. 2-bosqich — Arxitektura

Quyida tizim uchun 2 ta arxitektura varianti keltirilgan. Har biriga sxema, afzallik va kamchiliklar yozildi.

Variant A — Monolit (Single-page App + REST API, bitta server)
Sxema (soddalashtirilgan):
[Browser SPA (React/Vue)] <--HTTPS--> [Backend (Node/Express)] <---> [Postgres DB]
                                      \
                                       --> [SMTP/Push service (third-party)]

Afzalliklari:
- Tezroq boshlash va prototiplash — bitta kod bazasi.
- Lokal rivojlantirish va deploy soddaligi (bitta VPS yoki Heroku dyno).
- Kamroq infra murakkabligi — talabnoma uchun yetarli.

Kamchiliklari:
- Katta foydalanuvchi yukida skeyl qilish qiyinroq (whole appni oshirish kerak).
- Kod bazasi o'sgani sari modulni alohida boshqarish qiyinlashadi.

Qaysi hollarda yaxshi:
- Ta'lim loyihasi va tez demonstratsiya kerak bo‘lganda — ideal.

Variant B — Serverless / Jamstack arxitekturasi
Sxema (soddalashtirilgan):
[Browser SPA (static, Netlify/Vercel/CloudFront)] 
    <-- HTTPS --> [Serverless Functions (AWS Lambda / Vercel Functions)] 
            <---> [Managed DB (Aurora Serverless / DynamoDB)] 
            \
             --> [Auth (Auth0 / Firebase Auth)]
             --> [Push/Email (SendGrid / SNS)]

Afzalliklari:
- Infrani boshqarish kam — hosting va funksiyalar managed.
- Auto-scaling — trafikga mos ravishda to‘g‘ridan-to‘g‘ri moslashadi.
- Tezroq globallashuv (CDN orqali statik kontent).

Kamchiliklari:
- Boshlashda konfiguratsiya (provider, CORS, cold starts) biroz murakkab.
- Ba'zi xususiyatlar (ma'lumotlar migratsiyasi, tranzaksiyalar) uchun qo‘shimcha dizayn zarur.
- Ta'lim loyihasida barcha serverless kontseptsiyalarni tushuntirish va sozlash vaqt talab qiladi.

Qaysi hollarda yaxshi:
- Kichikdan o‘rta hajmgacha bo‘lgan foydalanuvchi bazasi va zarur bo‘lsa tez kengaytirish.

Qaysi biri yaxshiroq va nima uchun
- Ta'lim loyihasi va cheklangan vaqtda Variant A (Monolit SPA + REST) tavsiya etiladi: oddiyroq, tezroq prototiplash, kamroq infra sozlash va o‘rganish overheadi.

5. Texnologiya takliflari (minimal)
- Frontend: React yoki Vue (Create React App / Vite)
- Backend: Node.js + Express yoki Python FastAPI
- DB: PostgreSQL (yoki SQLite boshlash uchun)
- Autentifikatsiya: JWT yoki OAuth (Google) / Firebase Auth
- Deploy: Heroku/Render yoki VPS (DigitalOcean) — boshlanish uchun bepul tier yoki arzon variant.

6. Minimal MVP (ishlab chiqarish uchun 1-haftalik yo‘l xaritasi)
1. Login/Signup (email) + dashboard UI.
2. Habit CRUD va kunlik check-in formasi.
3. Progress sahifasi (haftalik chart — oddiy bar chart).
4. Deploy (Heroku + Postgres) va README bilan demo.
5. Qo‘shimcha: daily reminder email (SendGrid) — optional.

7. Yakun — Nima qilindi va keyingi qadamlar
Men mavzuni tanladim (Personal Habit Tracker), loyihaning 5 funksional va 5 nofunksional talabini, ikki arxitektura variantini va tavsiyalarni tayyorladim. Keyingi qadam — siz faylni repoga yuklab, kodga kirish va MVP funksiyalaridan birini (masalan Habit CRUD + check-in) amalga oshirish.