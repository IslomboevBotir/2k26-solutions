# Local News Aggregator — Tizim talablari va arxitekturasi

## Loyiha mavzusi: Local News Aggregator

Bu loyiha — mahalliy yangiliklar saytlaridan xabarlarni yig'ib, bir joyda foydalanuvchilarga ko'rsatib beradigan platforma. Ya'ni, har xil saytlarga kirib o'tirmasdan, bitta joyda barcha mahalliy yangiliklarni o'qish mumkin bo'ladi.

---

## 1-bosqich. Talablar

### 5 ta funksional talab

1. **Yangiliklarni avtomatik yig'ish** — Tizim mahalliy yangiliklar saytlaridan RSS yoki API orqali yangiliklarni o'zi yig'ib turadi. Qo'lda hech narsa qilish kerak emas.

2. **Kategoriyalarga ajratish** — Yangiliklar o'z mavzusiga qarab avtomatik tartiblanadi. Masalan: siyosat, sport, texnologiya, iqtisodiyot va boshqalar.

3. **Ro'yxatdan o'tish va profil** — Foydalanuvchi ro'yxatdan o'tadi, o'ziga yoqadigan kategoriyalarni tanlaydi va yoqtirgan yangiliklarini saqlab qo'yadi.

4. **Qidirish va filtrlash** — Foydalanuvchi yangiliklarni so'z, sana yoki kategoriya bo'yicha qidirib topa oladi.

5. **Bildirishnoma yuborish** — Foydalanuvchiga qiziqarli yangi yangilik chiqsa, telefoniga yoki brauzeriga xabar keladi.

### 5 ta nofunksional talab

1. **Tez ishlashi** — Sahifa 2 soniyadan ko'proq yuklanmasligi kerak. Odamlar kutishni yoqtirmaydi.

2. **Ko'p foydalanuvchini ko'tarishi** — Tizim bir vaqtda 10,000 dan ortiq odam ishlatganda ham qotmasligi kerak.

3. **Doimiy ishlashi** — Sayt deyarli har doim (99.9%) ishlab turishi kerak. Yangilik yig'ish ham to'xtab qolmasligi kerak.

4. **Xavfsizlik** — Foydalanuvchilarning ma'lumotlari (login, parol) shifrlangan holda saqlanishi kerak. Barcha aloqa HTTPS orqali bo'ladi.

5. **Qulay dizayn** — Sayt telefonda ham, kompyuterda ham chiroyli va qulay ko'rinishi kerak. Til sozlamalarida O'zbek, Rus va Ingliz tillari bo'ladi.

---

## 2-bosqich. Arxitektura

### 1-variant: Monolit arxitektura

Bu variantda butun tizim bitta serverda, bitta dastur sifatida ishlaydi.

#### Tizim sxemasi

```
Foydalanuvchi (Brauzer / Telefon)
        │
        │ (internet orqali)
        ▼
┌───────────────────────────┐
│     Frontend (veb-sayt)   │
│     React yoki Vue.js     │
└─────────────┬─────────────┘
              │
              ▼
┌───────────────────────────────────┐
│         Backend (bitta)           │
│                                   │
│  ┌─────────────────────────┐     │
│  │ - Yangilik yigish       │     │
│  │ - Foydalanuvchi boshq.  │     │
│  │ - Kategoriyalash        │     │
│  │ - Bildirishnoma         │     │
│  └─────────────────────────┘     │
└─────────────┬─────────────────────┘
              │
              ▼
┌───────────────────────────┐
│   Ma'lumotlar bazasi      │
│   (PostgreSQL)            │
└───────────────────────────┘
```

#### Afzalliklari:

- Tuzilishi juda sodda — hamma narsa bitta joyda
- Boshlanishida tez qilinadi — ko'p vaqt ketmaydi
- Testlash oson — bitta dasturni tekshirish qiyin emas
- Arzon — bitta server yetarli

#### Kamchiliklari:

- Kattalashganda qiyin bo'ladi — faqat serverni kuchaytirish orqali kattalashtirish mumkin
- Hamma narsa bir xil texnologiyada bo'ladi — boshqasini ishlatib bo'lmaydi
- Agar bitta joyi buzilsa, butun sayt ishlamay qoladi
- Katta jamoa bilan ishlash noqulay — kodlar aralashib ketadi

---

### 2-variant: Mikroservis arxitektura

Bu variantda tizim bir nechta kichik dasturlarga bo'lingan. Har bir dastur o'z vazifasini bajaradi va alohida ishlaydi.

#### Tizim sxemasi

```
Foydalanuvchi (Brauzer / Telefon)
        │
        ▼
┌───────────────────────────────┐
│       API Gateway             │
│  (hamma so'rov shu yerdan     │
│   o'tadi, yo'naltiradi)       │
└───┬─────┬──────┬──────┬───────┘
    │     │      │      │
    ▼     ▼      ▼      ▼
 ┌──────┐┌──────┐┌──────┐┌───────┐
 │Web   ││Yangi-││Foyd. ││Bildir. │
 │sayt  ││lik   ││servis││servis │
 │      ││servis││      ││       │
 └──┬───┘└──┬───┘└──┬───┘└───┬───┘
    │       │       │        │
    ▼       ▼       ▼        ▼
 ┌────┐  ┌─────┐  ┌──────┐ ┌─────┐
 │ DB │  │ DB  │  │Elast │ │Redis│
 │    │  │     │  │icSrch│ │kesh │
 └────┘  └─────┘  └──────┘ └─────┘

         ┌──────────────────┐
         │ (servislar bir-  │
         │  biri bilan      │
         │  gaplashadi)     │
         └──────────────────┘
```

#### Afzalliklari:

- Har bir qismni alohida kattalashtirish mumkin — masalan, faqat yangilik yigishni kuchaytirish
- Har bir servis o'z texnologiyasini ishlatishi mumkin — qidiruv uchun Elasticsearch, kesh uchun Redis
- Agar bitta servis buzilsa, qolganlari ishlayveradi — sayt butunlay to'xtab qolmaydi
- Har bir jamoa a'zosi alohida qism ustida ishlashi mumkin
- Yangi funksiya qo'shganda butun saytni to'xtatish shart emas

#### Kamchiliklari:

- Tuzilishi murakkab — ko'p narsalarni boshqarish kerak
- Qimmatroq — ko'p server kerak bo'ladi
- Har bir servisni alohida kuzatish kerak
- Ma'lumotlarni bir-biriga moslab turish qiyinroq

---

### Qaysi variant yaxshiroq?

**Mikroservis arxitektura (2-variant) yaxshiroq**, sababi quyidagilar:

1. **O'sish uchun qulay** — Loyiha kattalashganda yangilik yigish, qidiruv, bildirishnoma kabi qismlarni alohida kuchaytirish mumkin. Monolitda butun serverni almashtirishga to'g'ri keladi.

2. **Ishonchli** — Tasavvur qiling, bildirishnoma xizmati buzildi. Mikroservisda qolgan qismlar ishlayveradi — foydalanuvchilar yangilik o'qiyveradi. Monolitda esa butun sayt to'xtaydi.

3. **To'g'ri texnologiya tanlash** — Qidiruv funksiyasi uchun maxsus dastur (Elasticsearch), keshlash uchun Redis ishlatish mumkin. Monolitda hamma narsa bitta texnologiyada bo'ladi.

4. **Jamoa bilan ishlash** — Bir dasturchi yangilik yigish bilan, ikkinchisi foydalanuvchi qismi bilan ishlashi mumkin. Bir-birining kodiga aralashmaydi.

**Lekin:** Agar loyiha endi boshlanayotgan bo'lsa va odam kam bo'lsa, **monolitdan boshlash** ham yaxshi fikr. Keyin loyiha o'sganda mikroservislarga o'tish mumkin. Ko'p katta kompaniyalar (masalan, Uber, Netflix) shunday qilishgan.

---

## Xulosa

Bu ishda Local News Aggregator uchun 5 ta funksional va 5 ta nofunksional talab yozildi. Keyin monolit va mikroservis arxitektura variantlari ko'rib chiqildi. Loyiha uchun mikroservis arxitekturasi tavsiya etildi, chunki u o'sish, ishonchlilik va jamoa ishi uchun qulayroq.
