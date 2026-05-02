# Language Exchange Platform — Tizim talablari va arxitekturasi

## Loyiha mavzusi: Language Exchange Platform

Bu loyiha turli tillarni o'rganayotgan foydalanuvchilarni bir platformada uchrashtiradi. Foydalanuvchilar o'zaro chat, audio yoki video suhbat orqali mashq qiladi, sherik topadi va til ko'nikmalarini rivojlantiradi.

---

## 1-bosqich. Talablar

### 5 ta funksional talab

1. **Ro'yxatdan o'tish va profil yaratish** — Foydalanuvchi email yoki telefon orqali ro'yxatdan o'tadi, qaysi tilni bilishi va qaysi tilni o'rganmoqchi ekanini profilida ko'rsatadi.

2. **Til sherigini topish** — Tizim foydalanuvchining tili, darajasi, qiziqishlari va vaqt zonasiga qarab mos suhbatdoshlarni tavsiya qiladi.

3. **Matnli, audio va video muloqot** — Foydalanuvchilar platforma ichida yozishma, ovozli qo'ng'iroq va video suhbat qila oladi.

4. **Dars va mashg'ulot rejalashtirish** — Foydalanuvchi boshqa sherik bilan suhbat vaqti belgilashi, eslatma qo'yishi va sessiya tarixini ko'rishi kerak.

5. **Baholash va fikr bildirish** — Har bir suhbatdan keyin foydalanuvchilar bir-biriga reyting va qisqa fikr qoldira oladi, bu ishonchli hamkorlarni tanlashga yordam beradi.

### 5 ta nofunksional talab

1. **Yuqori tezlik** — Asosiy sahifalar 2 soniyadan oshmagan vaqtda yuklanishi, chat xabarlari esa deyarli real vaqt rejimida yetib borishi kerak.

2. **Masshtablanuvchanlik** — Tizim bir vaqtning o'zida kamida 20,000 faol foydalanuvchini qo'llab-quvvatlashi kerak.

3. **Ishonchlilik** — Platforma 99.9% mavjudlik bilan ishlashi, nosozlik yuz bersa asosiy xizmatlar tez tiklanishi kerak.

4. **Xavfsizlik va maxfiylik** — Parollar xeshlangan holda saqlanadi, trafik HTTPS orqali uzatiladi va foydalanuvchi shaxsiy ma'lumotlari himoyalanadi.

5. **Ko'p platformalilik** — Tizim telefon, planshet va kompyuterda qulay ishlashi, kamida O'zbek, Ingliz va Rus tillarini qo'llashi kerak.

---

## 2-bosqich. Arxitektura

### 1-variant: Monolit arxitektura

Bu variantda frontend, backend logikasi, matching, chat boshqaruvi va foydalanuvchi modullari bitta backend ilova ichida ishlaydi.

#### Tizim sxemasi

```text
Foydalanuvchi (Web / Mobile)
         │
         ▼
┌───────────────────────────┐
│   Frontend (React / App)  │
└─────────────┬─────────────┘
              │ HTTPS
              ▼
┌────────────────────────────────────┐
│         Monolit Backend            │
│                                    │
│ - Auth va profil                   │
│ - Matching                         │
│ - Chat va qo'ng'iroq boshqaruvi    │
│ - Sessiya rejalashtirish           │
│ - Reyting va fikrlar               │
└─────────────┬──────────────────────┘
              │
              ▼
┌───────────────────────────┐
│ PostgreSQL / Redis        │
└───────────────────────────┘
```

#### Afzalliklari

- Tuzilishi sodda va tez ishlab chiqiladi
- Kichik jamoa uchun boshqarish oson
- Test va deploy jarayoni yengilroq
- Boshlang'ich xarajat past bo'ladi

#### Kamchiliklari

- Foydalanuvchilar soni oshsa butun tizimni birga masshtablashga to'g'ri keladi
- Chat va video kabi og'ir qismlar umumiy backendga yuk beradi
- Bitta moduldagi xato butun tizimga ta'sir qilishi mumkin
- Katta jamoa uchun kod bazasi noqulaylashadi

---

### 2-variant: Mikroservis arxitektura

Bu variantda tizim vazifalarga qarab bir nechta mustaqil servisga bo'linadi: autentifikatsiya, matching, chat, media va bildirishnoma servislar.

#### Tizim sxemasi

```text
Foydalanuvchi (Web / Mobile)
         │
         ▼
┌───────────────────────────┐
│       API Gateway         │
└───┬────────┬───────┬──────┘
    │        │       │
    ▼        ▼       ▼
┌───────┐ ┌───────┐ ┌──────────┐
│ Auth  │ │Match  │ │ Chat     │
│Service│ │Service│ │ Service  │
└───┬───┘ └───┬───┘ └────┬─────┘
    │         │          │
    ▼         ▼          ▼
┌───────┐ ┌────────┐ ┌──────────┐
│User DB│ │Match DB│ │Message DB│
└───────┘ └────────┘ └──────────┘

        ┌──────────────────────┐
        │ Media Service        │
        │ Notification Service │
        │ Redis / Queue        │
        └──────────────────────┘
```

#### Afzalliklari

- Har bir servisni alohida masshtablash mumkin
- Chat va video kabi real vaqt servislarini mustaqil optimallashtirish oson
- Nosozlik bitta servis ichida cheklanib qolishi mumkin
- Katta jamoa parallel ishlashi qulayroq
- Har bir modul uchun mos texnologiya tanlash mumkin

#### Kamchiliklari

- Tuzilishi murakkabroq va sozlash ko'proq vaqt oladi
- Deploy, monitoring va log yuritish qiyinlashadi
- Servislararo aloqa sabab kechikish va integratsiya muammolari bo'lishi mumkin
- Infrastrukturaga ketadigan xarajat ko'proq bo'ladi

---

### Qaysi variant yaxshiroq?

**Mikroservis arxitektura yaxshiroq**, chunki language exchange platformada chat, audio/video qo'ng'iroq, matching va bildirishnoma kabi yuklamasi har xil bo'lgan modullar mavjud. Ularni alohida servis qilish tizimni osonroq kengaytiradi va real vaqt funksiyalarini yaxshiroq boshqarishga yordam beradi.

Shu bilan birga, agar loyiha endi boshlanayotgan bo'lsa va jamoa kichik bo'lsa, avval **monolit** bilan ish boshlash ham amaliy yechim bo'ladi. Lekin uzoq muddatli rivojlanish uchun **mikroservis** varianti maqsadga muvofiqroq.

---

## Xulosa

Bu ishda `Language Exchange Platform` uchun 5 ta funksional va 5 ta nofunksional talab yozildi. Shuningdek, monolit va mikroservis arxitektura variantlari solishtirildi. Platformaning real vaqt chat va video funksiyalari sabab mikroservis arxitekturasi tavsiya qilindi.
