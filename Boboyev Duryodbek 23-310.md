
# Resume Builder Service

## Tizim talablari va arxitekturasi

**Bajardi:** Duryodbek

---

## Maqsad

* Tizim talablarini aniqlash
* Mos arxitektura tanlash
* Arxitektura variantlarini taqqoslash
* Natijani Markdown formatida tayyorlash

---

## 1-bosqich: Talablar

### Loyiha tavsifi

<!-- Men tanlagan loyiha — **Resume Builder Service**. Ushbu tizim foydalanuvchilarga professional rezyume (CV) yaratish, tahrirlash va yuklab olish imkonini beradi. -->

---

### Funksional talablar

1. Foydalanuvchi tizimda ro‘yxatdan o‘tishi va login qilishi kerak.
2. Foydalanuvchi yangi rezyume yaratishi va mavjudini tahrirlashi kerak.
3. Tizim turli xil dizayn shablonlarini (template) taqdim etishi kerak.
4. Foydalanuvchi rezyumeni PDF formatda yuklab olishi kerak.
5. Foydalanuvchi o‘z ma’lumotlarini (tajriba, ta’lim, ko‘nikmalar) qo‘shishi kerak.

---

### Nofunksional talablar

1. Tizim tez ishlashi kerak (javob vaqti 2 soniyadan oshmasligi kerak).
2. Tizim xavfsiz bo‘lishi kerak (JWT, HTTPS orqali himoya).
3. Tizim kengaytiriladigan (scalable) bo‘lishi kerak.
4. Tizim responsiv dizaynga ega bo‘lishi kerak (mobil qurilmalarda ishlashi).
5. Tizim barqaror ishlashi kerak (yuqori uptime).

---

## 2-bosqich: Arxitektura

# 🔹 1-variant: MVC arxitekturasi (.NET MVC)

### Tizim sxemasi

```id="mvc1"
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
```

---

### Tavsif

Men tanlagan birinchi arxitektura — **MVC (Model-View-Controller)**. Bu arxitekturada tizim uch qismga bo‘linadi:

* Controller — so‘rovlarni boshqaradi
* Model — ma’lumotlar va logikani saqlaydi
* View — foydalanuvchiga natijani ko‘rsatadi

---

### Afzalliklari

* Oddiy va tushunarli
* Tez ishlab chiqish mumkin
* Kichik loyihalar uchun qulay
* Debug qilish oson

---

### Kamchiliklari

* Business logic Controller ichida aralashib ketadi
* Katta loyihalarda boshqarish qiyin
* Test yozish murakkab
* Kod strukturasida tartib buzilishi mumkin

---

# 🔹 2-variant: Clean Architecture

### Tizim sxemasi

```id="clean1"
+--------------------------------------------------+
|                CLIENT (Angular SPA)              |
+--------------------------------------------------+
                      |
                    HTTPS
                      v
+--------------------------------------------------+
|                PRESENTATION LAYER                |
|            (Controllers / API)                  |
+--------------------------------------------------+
                      |
                      v
+--------------------------------------------------+
|                APPLICATION LAYER                |
|          (Use Cases / Services)                 |
+--------------------------------------------------+
                      |
                      v
+--------------------------------------------------+
|                DOMAIN LAYER                     |
|        (Entities / Business Rules)              |
+--------------------------------------------------+
                      |
                      v
+--------------------------------------------------+
|                INFRASTRUCTURE LAYER             |
|     (Database, External Services)               |
+--------------------------------------------------+
                      |
                      v
+--------------------------------------------------+
|                 DATABASE (SQL Server)           |
+--------------------------------------------------+
```

---

### Tavsif

Ikkinchi variant sifatida men **Clean Architecture** ni tanladim. Bu arxitekturada tizim qatlamlarga bo‘linadi va har bir qatlam aniq vazifani bajaradi.

Asosiy tamoyil:
➡️ Bog‘liqlik ichki qatlamlarga qarab yo‘naladi

---

### Afzalliklari

* Katta tizimlar uchun juda qulay
* Test qilish oson
* Business logic alohida ajratilgan
* Kodni kengaytirish oson
* Kam bog‘liqlik (loose coupling)

---

### Kamchiliklari

* Murakkabroq
* Ko‘proq vaqt talab qiladi
* Kichik loyihalar uchun ortiqcha bo‘lishi mumkin
* Kod hajmi katta bo‘ladi

---

## Qaysi biri yaxshiroq?

Menimcha:

* Agar loyiha kichik yoki boshlang‘ich bosqichda bo‘lsa → **MVC yaxshiroq**
* Agar loyiha katta va professional bo‘lsa → **Clean Architecture yaxshiroq**

---

## Yakuniy xulosa

Men ushbu loyiha uchun boshlanishida **MVC arxitekturasini tanlashni ma’qul deb hisoblayman**, chunki u sodda va tez ishlab chiqiladi. Keyinchalik loyiha kengayganda uni **Clean Architecture** ga o‘tkazish mumkin.

