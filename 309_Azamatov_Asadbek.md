# Loyiha: Podcast nashriyot platformasi

## 1-bosqich: Talablar

### Funksional talablar

1. Tizim podcast mualliflariga audio fayllarni yuklash, sarlavha, tavsif, muqova rasmi va teglar qo'shib epizodlarni nashr etish imkonini berishi kerak.
2. Tizim tinglovchilarga podcastlarni qidirish, kategoriyalar bo'yicha filtrlash, obuna bo'lish va epizodlarni to'g'ridan-to'g'ri platforma ichida yoki yuklab olib tinglash imkonini berishi kerak.
3. Tizim mualliflarga eshitishlar soni, tinglovchilar geografiyasi, o'rtacha tinglash davomiyligi va obunalar o'sishi kabi analitika ma'lumotlarini ko'rsatishi kerak.
4. Tizim podcast RSS tasmalarini avtomatik ravishda yaratishi va Spotify, Apple Podcasts hamda Google Podcasts kabi tashqi platformalar bilan integratsiya qilishi kerak.
5. Tizim foydalanuvchilarga podcastlarga sharh qoldirish, reytinglar berish va epizodlarni ijtimoiy tarmoqlarda ulashish imkonini berishi kerak.

### Nofunksional talablar

1. Tizim audio fayllarni yuklashda 99.9% ishonchliligi bilan ishlashi va 500 MB gacha bo'lgan fayllarni 60 soniya ichida qayta ishlashi kerak.
2. Tizim bir vaqtning o'zida 10 000 dan ortiq aktiv tinglovchini kutilish vaqtisiz audio oqimini uzluksiz ta'minlab boshqarishi kerak.
3. Tizim yuklangan audio fayllarni turli qurilmalar uchun avtomatik ravishda bir nechta formatga (MP3, AAC, OGG) va sifat darajasiga (128 kbps, 192 kbps, 320 kbps) o'zgartirishi kerak.
4. Tizim GDPR va CCPA talablariga muvofiq foydalanuvchi ma'lumotlarini himoya qilishi, audio kontentni ruxsatsiz yuklab olishdan CDN darajasida muhofaza qilishi kerak.
5. Tizim muallif, tinglovchi va administrator rollarini qo'llab-quvvatlab, har bir rol uchun alohida kirish huquqlari va boshqaruv panelini taqdim etishi kerak.

---

## 2-bosqich: Arxitektura

### 1-variant: Monolit arxitektura

#### Tizim sxemasi

```
Muallif ilovasi (Web/Mobil)
        |
        v
Monolit Backend (Fastapi)
        |
   _____|______________________________________
   |          |            |                  |
   v          v            v                  v
Foydalanuvchi  Audio      Analitika       RSS/Integratsiya
   moduli    moduli        moduli            moduli
        |          |
        v          v
  Birlamchi    Fayl tizimi / S3
  Ma'lumotlar     (Audio fayllar)
     Bazasi
  (PostgreSQL)
        |
        v
  CDN (CloudFront)
        |
        v
  Tinglovchi ilovasi (Web/Mobil)
```

#### Tavsif

Bu arxitekturada butun platforma bitta monolitik dastur sifatida ishlab chiqiladi. Foydalanuvchi boshqaruvi, audio yuklash va qayta ishlash, analitika, RSS generatsiyasi va barcha boshqa funksiyalar bitta backend ilovasida to'plangan. Audio fayllar S3 yoki shunga o'xshash ob'ekt xotiraga yuklangach, CDN orqali tinglovchilarga yetkaziladi. Barcha metadata va foydalanuvchi ma'lumotlari bitta PostgreSQL bazasida saqlanadi.

#### Afzalliklari

- Ishlab chiqish va mahalliy muhitda test qilish ancha osonroq va tezroq.
- Bitta deployment pipeline, bitta server infratuzilmasi — texnik xizmat xarajatlari kamroq.
- Barcha modullar o'rtasidagi ma'lumot almashinuvi to'g'ridan-to'g'ri funksiya chaqiriqlari orqali amalga oshiriladi, tarmoq kechikishlari yo'q.
- Kichik startap yoki MVP (Minimum Viable Product) bosqichi uchun ideal — tez bozorga chiqish mumkin.
- DevOps va infratuzilma tajribasi kam bo'lgan kichik jamoalar uchun boshqarish qulay.

#### Kamchiliklari

- Audio transkodlash kabi resurs talab qiladigan jarayonlar butun ilovani sekinlashtirishi mumkin.
- Platformaning bitta qismiga katta yuklama tushsa (masalan, mashhur podcast virusga aylanib ketsa), faqat o'sha qismni emas, butun tizimni kengaytirishga to'g'ri keladi.
- Kod bazasi kattalashgani sari modullar orasidagi bog'liqliklar murakkablashib, yangi funksiyalar qo'shish qiyinlashadi.
- Audio streaming va analitika kabi turli talab xususiyatlarini alohida optimallashtirish imkoni yo'q.
- Deployment jarayonida to'liq ilovani to'xtatish kerak bo'lishi mumkin, bu esa foydalanuvchilar uchun qisqa uzilishlarga olib keladi.

---

### 2-variant: Mikroservis arxitektura

#### Tizim sxemasi

```
Muallif ilovasi          Tinglovchi ilovasi
(Web/Mobil)              (Web/Mobil)
       \                      /
        \                    /
         v                  v
         API Gateway (Kong / AWS API Gateway)
              |      |      |      |
              v      v      v      v
        [Auth   ] [Media ] [Feed  ] [Analytics]
        [Servis ] [Servis] [Servis] [Servis   ]
              |      |      |      |
              v      v      v      v
        [Users ] [Audio ] [Obuna ] [Eventlar ]
        [DB    ] [Store ] [DB    ] [DB       ]
        (Postgres)(S3+CDN)(Postgres)(ClickHouse)

                   |
                   v
           [Transcode Servis]
           (FFmpeg Worker Pool)
                   |
                   v
           [Xabar Navbati]
           (RabbitMQ / Kafka)
                   |
          _________|_________
          |                  |
          v                  v
   [Notification      [RSS Generator
     Servis]            Servis]
```

#### Tavsif

Bu arxitekturada platforma bir nechta mustaqil servislarga bo'linadi. Auth servisi autentifikatsiya va avtorizatsiyani boshqaradi. Media servisi audio fayllarni qabul qiladi va transcode servisiga navbat orqali vazifa yuboradi. Transcode servisi FFmpeg yordamida audio fayllarni turli formatlarga o'girib, CDN ga yuklaydi. Feed servisi podcastlar ro'yxati va epizodlarni taqdim etadi. Analytics servisi tinglovchi ma'lumotlarini alohida ClickHouse ma'lumot omboriga yig'adi. Barcha servislar API Gateway orqali muloqot qiladi, asinxron jarayonlar uchun esa xabar navbati (Kafka yoki RabbitMQ) ishlatiladi.

#### Afzalliklari

- Transcode, streaming va analitika servislarini platformaning boshqa qismlariga ta'sir qilmasdan alohida kengaytirish mumkin.
- Bitta servis ishdan chiqsa (masalan, analitika), qolgan funksiyalar — audio yuklash, tinglash, obuna — uzluksiz davom etadi.
- Har bir servis o'z texnologiya to'plamidan foydalanishi mumkin: transcode uchun Python+FFmpeg, analitika uchun Go, frontend API uchun Node.js.
- Katta jamoalarda turli dasturchilar guruhlari mustaqil ravishda turli servislarni ishlab chiqishi mumkin.
- Kelajakda ko'p mintaqali deployment va global CDN optimizatsiyasini amalga oshirish ancha osonroq.

#### Kamchiliklari

- Loyihalash, ishlab chiqish va sinovdan o'tkazish ancha murakkab — servislararo kontraktlar, versiyalash va tarqatilgan tracing kerak bo'ladi.
- Ko'proq DevOps infratuzilmasi: Kubernetes, servis-to-servis TLS, monitoring dashboardlar va hokazo.
- Servislar orasida ma'lumotlar izchilligini (distributed transactions) ta'minlash texnik jihatdan qiyin.
- Mahalliy ishlab chiqish muhitida barcha servislarni bir vaqtda ishga tushirish noqulay bo'lishi mumkin.
- Yangi dasturchi uchun arxitekturani tushunish va unga moslashish ko'proq vaqt talab etadi.

---

## Yakuniy qaror

**Ushbu loyiha uchun mikroservis arxitektura yaxshiroq tanlov hisoblanadi.**

Podcast platformasining asosiy texnik muammosi — audio transakodlash. Bu jarayon CPU va xotira jihatdan juda resurs talab qiladi va foydalanuvchilar soni oshishi bilan keskin o'sadi. Monolit arxitekturada bu og'ir jarayon barcha boshqa funksiyalar — tinglash, qidirish, analitika — bilan bir xil serverlarda ishlaydi va ularni sekinlashtirishi mumkin. Mikroservis arxitekturasida esa transcode servisi alohida worker pool sifatida faqat kerak bo'lganda kengaytiriladi.

Bundan tashqari, podcastlar uchun yuklama noodatiy taqsimlangan: yangi epizod chiqqanda bir necha daqiqada minglab foydalanuvchi ulanishi mumkin. Monolit tizimda butun ilovani kengaytirish kerak bo'lsa, mikroservis tizimda faqat streaming va CDN qatlamini kengaytirish yetarli. Analytics uchun ClickHouse kabi maxsus ma'lumot omborini alohida servisga ulash ham monolit tizimda amaliy emas.

Boshlang'ich MVP bosqichida "modular monolit" yondashuvi qo'llanilishi mumkin — ya'ni kod bazasi mikroservislarga tayyorlangan modullar shaklida yoziladi, lekin dastlab bitta deployment sifatida ishga tushiriladi. Platforma o'sishi va yuklama oshishi bilan individual modullar alohida servislarga ajratiladi. Bu yondashuv dastlabki murakkablikni kamaytirgan holda, kelajakdagi arxitektura o'zgarishlarini osonlashtiradi.
