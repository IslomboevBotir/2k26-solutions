<!-- Book Club Community Site -->

Guruh:  23-310
Talaba: Raimjonov Muhammadali 
Fan: Advanced Programming



 1-bosqich: Talablar

 Funksional Talablar

1. Foydalanuvchilar ro'yxatdan o'tib, tizimga kira oladi (login/register)
2. Foydalanuvchilar kitob qo'sha oladi va qidira oladi
3. Kitoblarga sharh va fikr yoza oladi
4. Kitob klubi yaratib, do'stlarni taklif qila oladi
5. Kitoblarga reyting bera oladi (1–5 yulduz)

 Nofunksional Talablar

1. Sahifa 2 sekunddan tez yuklanishi kerak
2. Parollar xavfsiz saqlanishi kerak (shifrlangan)
3. Tizim bir vaqtda 5000 foydalanuvchiga xizmat ko'rsata olishi kerak
4. Tizim 99% vaqt ishlayotgan bo'lishi kerak
5. Kod tushunarli va o'zgartirishga qulay bo'lishi kerak



 2-bosqich: Arxitektura



 Variant 1: Monolit Arxitektura

 Sxema:
https://lucid.app/lucidchart/94a59f1d-5cff-4d58-aae9-da1ec97b9a0b/edit?viewport_loc=-447%2C48%2C2679%2C1388%2C0_0&invitationId=inv_085c10e0-5e1e-4704-8980-5e9fc1a7f74a

 Afzalliklari
- Ishlab chiqish oson va tez
- Deploy qilish oddiy (bitta server)
- Kichik jamoa uchun qulay

 Kamchiliklari
- Tizim kattalashganda boshqarish qiyinlashadi
- Bitta xato butun saytni to'xtatib qo'yishi mumkin
- Miqyoslanish cheklangan



 Variant 2: Mikroservislar Arxitekturasi

 Sxema:
https://lucid.app/lucidchart/deb8b4b0-5728-40c1-b49f-77127d5e83d4/edit?viewport_loc=-460%2C62%2C2547%2C1319%2C0_0&invitationId=inv_3cdacf75-4533-44d6-bb2a-fd31955f6731

 Afzalliklari
- Har bir qism mustaqil ishlaydi
- Bitta servis to'xtasa boshqalari davom etadi
- Katta loyihalar uchun yaxshi

 Kamchiliklari
- Sozlash va boshqarish murakkab
- Kichik loyiha uchun ortiqcha
- Xarajat yuqori



 Yakuniy Tanlov: Variant 1 — Monolit

Sababi:  
Book Club saytiga boshlang'ich bosqichda monolit arxitektura yetarli. Loyiha kichik, jamoa kichik, foydalanuvchi soni ham hozircha ko'p emas. Monolit bilan tez ishlab chiqish va deploy qilish mumkin. Keyinchalik sayt kattalashsa, mikroservislarga o'tish mumkin.
