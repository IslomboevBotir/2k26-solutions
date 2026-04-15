# Loyiha: Restoran oshxonasi buyurtmalarini kuzatish tizimi

## 1-bosqich: Talablar

### Funksional talablar

1. Tizim ofitsiant yoki kassirga yangi mijoz buyurtmasini yaratish va uni oshxonaga yuborish imkonini berishi kerak.
2. Tizim oshxona xodimlariga faol buyurtmalarni stol raqami, taom nomi, miqdori, izohlar va buyurtma vaqti bilan ko'rsatishi kerak.
3. Tizim oshxona xodimlariga buyurtma holatini Qabul qilindi, Tayyorlanmoqda, Tayyor yoki Yetkazildi holatlariga o'zgartirish imkonini berishi kerak.
4. Tizim har bir buyurtmaning tayyorlanish vaqtini kuzatishi va kechikayotgan buyurtmalarni ko'rsatishi kerak.
5. Tizim buyurtma Tayyor holatiga o'tkazilganda ofitsiantga xabar berishi kerak.

### Nofunksional talablar

1. Tizim buyurtma malumotlarini taxminan 1 yoki 2 soniya ichida yangilashi kerak.
2. Tizim restoran gavjum bo'lgan vaqtda ham ishonchli ishlashi va buyurtmalarni yo'qotmasligi kerak.
3. Tizim gril, ichimliklar, desert va asosiy taom kabi bir nechta oshxona bo'limlarini qo'llab-quvvatlashi kerak.
4. Tizim interfeysi oshxona xodimlari uchun sodda, tushunarli va o'qishga qulay bo'lishi kerak.
5. Tizim ofitsiantlar, oshxona xodimlari va menejerlar uchun login hamda foydalanuvchi rollaridan foydalanishi kerak.

## 2-bosqich: Arxitektura

### 1-variant: Monolit arxitektura

#### Tizim sxemasi

Ofitsiant yoki kassir ilovasi -> Monolit backend -> Malumotlar bazasi

Oshxona ekrani -> Monolit backend -> Malumotlar bazasi

Menejer paneli -> Monolit backend -> Malumotlar bazasi

#### Tavsif

Bu arxitekturada butun tizim bitta dastur sifatida ishlab chiqiladi. Bitta backend buyurtmalar, oshxona holatlari, xabarlar, foydalanuvchilar va hisobotlar bilan ishlaydi. Barcha malumotlar bitta malumotlar bazasida saqlanadi. Bu yondashuv bitta restoran yoki kichik restoran biznesi uchun qulay va tushunarli hisoblanadi.

#### Afzalliklari

- Ishlab chiqish va test qilish osonroq.
- Server va texnik xizmat xarajatlari kamroq bo'ladi.
- Barcha malumotlar bitta bazada saqlanadi.
- Kichik va o'rta restoranlar uchun mos keladi.
- Kichik dasturchilar jamoasi uchun tushunish oson.

#### Kamchiliklari

- Tizimning faqat bitta qismini alohida kengaytirish qiyin.
- Asosiy dastur ishlamay qolsa, butun tizim to'xtashi mumkin.
- Loyiha kattalashgani sari kodni boshqarish qiyinlashadi.
- Bitta modulni yangilash uchun butun dasturni qayta joylash kerak bo'lishi mumkin.
- Ko'p filialli katta restoran tarmog'i uchun eng yaxshi tanlov emas.

### 2-variant: Mikroservis arxitektura

#### Tizim sxemasi

Ofitsiant yoki kassir ilovasi -> API Gateway -> Buyurtma servisi -> Buyurtma bazasi

Oshxona ekrani -> API Gateway -> Oshxona servisi -> Oshxona bazasi

Menejer paneli -> API Gateway -> Foydalanuvchi va hisobot servisi -> Foydalanuvchi va hisobot bazasi

API Gateway -> Xabar berish servisi -> Xabarlar bazasi

#### Tavsif

Bu arxitekturada tizim bir nechta alohida servislarga bo'linadi. Buyurtma servisi mijoz buyurtmalari bilan ishlaydi, oshxona servisi buyurtma holatlarini boshqaradi, xabar berish servisi esa ofitsiantlarga xabar yuboradi. API Gateway ilovalardan kelgan so'rovlarni qabul qilib, kerakli servisga yo'naltiradi. Har bir servisni alohida o'zgartirish va kengaytirish mumkin.

#### Afzalliklari

- Har bir servisni alohida kengaytirish mumkin.
- Bitta servisdagi muammo har doim ham butun tizimni to'xtatmaydi.
- Yangi funksiyalarni qo'shish qulayroq bo'ladi.
- Katta restoran tarmoqlari uchun mos keladi.
- Zarur bo'lsa, turli servislar turli texnologiyalardan foydalanishi mumkin.

#### Kamchiliklari

- Loyihalash va ishlab chiqish murakkabroq.
- Ko'proq server va DevOps bilimlari kerak bo'ladi.
- Servislar orasidagi aloqa tizimni sekinlashtirishi mumkin.
- Servislar orasida malumotlar mosligini saqlash qiyinroq.
- Monolit tizimga qaraganda texnik xizmat narxi yuqoriroq.

## Yakuniy qaror

Ushbu loyiha uchun monolit arxitektura yaxshiroq tanlov hisoblanadi.

Restoran oshxonasi buyurtmalarini kuzatish tizimiga tez yangilanish, gavjum vaqtda barqaror ishlash va xodimlar uchun oddiy jarayon kerak. Bitta restoran yoki kichik restoran tarmog'i uchun yuklama odatda juda katta bo'lmaydi. Shuning uchun bitta backend asosiy vazifalarni bemalol bajara oladi. Real vaqtga yaqin yangilanishlarni monolit tizimda ham WebSocket kabi texnologiyalar orqali qo'shish mumkin.

Mikroservislar ko'p filialli, foydalanuvchilari ko'p va alohida jamoalar ishlaydigan katta tizimlar uchun foydaliroq. Bu loyihada esa bunday darajadagi kengaytirish zarur emas. Shu sababli monolit arxitektura amaliyroq, arzonroq va texnik xizmat ko'rsatish uchun osonroq hisoblanadi.
