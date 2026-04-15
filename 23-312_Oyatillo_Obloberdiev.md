### Onlayn Imtihon Proctoring Tizimi (Online Exam Proctoring System)

### 1-Bosqich

### 5 ta Funksional talab (Functional Requirements) 

1. Talaba identifikatsiyasi: Tizim imtihon boshlanishidan oldin yuzni tanish texnologiyasi (Face Recognition) orqali talaba shaxsini tasdiqlashi kerak.

2. Video/Audio monitoring: Imtihon davomida kamera va mikrofon orqali talabani real vaqtda kuzatish va yozib olish imkoniyati.

3. Ekran nazorati: Talabaning kompyuter ekranini monitoring qilish va boshqa ilovalarga yoki saytlarga o‘tishni taqiqlash (Lockdown browser funksiyasi).

4. AI-deteksiya: Sun'iy intellekt yordamida shubhali harakatlarni (masalan: ikkinchi shaxsning paydo bo‘lishi, telefondan foydalanish, kitob varaqlash) avtomatik aniqlash va belgilash.

5. Natijalar hisoboti: Imtihon yakunida har bir talaba uchun "ishonchlilik ko'rsatkichi" (Integrity Score) bilan birga batafsil hisobot tayyorlash.

### 5 ta Nofunksional talab (Non-functional Requirements) 

1. Masshtablanuvchanlik (Scalability): Tizim bir vaqtning o'zida kamida 5000 ta video oqimini (stream) uzilishlarsiz qabul qila olishi kerak.

2. Xavfsizlik (Security): Foydalanuvchilarning shaxsiy ma'lumotlari va video yozuvlar AES-256 algoritmi orqali shifrlanishi shart.

3. Kechikish (Latency): Video uzatish va AI tahlil qilish jarayonidagi kechikish 2-3 soniyadan oshmasligi lozim.

4. Mavjudlik (Availability): Imtihon mavsumida tizimning barqaror ishlash koeffitsiyenti 99.9% ni tashkil etishi kerak.

5. Platformalararo moslik: Tizim barcha zamonaviy brauzerlarda (Chrome, Firefox, Safari) qo'shimcha plaginlarsiz ishlashi shart.


### 2-Bosqich

### 1-variant: Monolitik arxitektura (Monolithic Architecture)

1. Tizim sxemasi: Barcha funksional qismlar (Auth, Video, AI, DB) bitta yagona kod bazasida va bitta serverda joylashadi.

2. Afzalliklari: Dastlabki bosqichda ishlab chiqish tez va arzon; testlash va serverga joylashtirish (deployment) oson.

3. Kamchiliklari: Tizimning bir qismi (masalan, video tahlil) ishdan chiqsa, butun tizim to'xtaydi; yuklama ortganda masshtablash (scaling) juda qiyin.

### Afzalliklari (+):

1. Oddiylik: Loyihani boshlash va dastlabki kodni yozish juda tez.

2. Oson testlash: Barcha funksiyalar bitta joyda bo'lgani uchun xatolarni topish osonroq.

3. Yuqori tezlik (aloqa): Servislararo tarmoq so'rovlari bo'lmagani uchun ma'lumot almashish tezroq bo'ladi.

### Kamchiliklari (-):

1. Moslashuvchanlik past: Yangi texnologiyaga o'tish uchun hamma narsani qaytadan yozish kerak.

2. Xavf darajasi: Agar bitta xato (bug) bo'lsa, butun imtihon tizimi to'liq o'chib qolishi mumkin.

3. Yiriklik: Kod ko'paygan sayin uni boshqarish va tushunish qiyinlashib boradi.

### 2-variant: Mikroservislar arxitekturasi (Microservices Architecture)

1. Tizim sxemasi: Tizim bir-biridan mustaqil kichik servislarga (User Service, Video Service, AI Analytics, Reporting) bo'linadi va ular API Gateway orqali bog'lanadi.

2. Afzalliklari: Yuqori barqarorlik (bir servis to'xtasa, qolganlari ishlayveradi); har bir servisni alohida masshtablash imkoniyati; turli tillarda yozish imkoniyati (masalan, AI qismi Python'da, Auth qismi Go'da).

3. Kamchiliklari: Tizimni sozlash va boshqarish ancha murakkab; servislar orasidagi aloqa (network call) kechikishlarga sabab bo'lishi mumkin.

### Afzalliklari (+):

1. Mustaqillik: Agar AI tahlili servisi yuklama tufayli sekinlashsa, talaba baribir ro'yxatdan o'tishi yoki savollarni ko'rishi mumkin.

2. Masshtablanuvchanlik: Proctoring tizimida minglab talabalar video uzatayotganda, faqatgina "Video Stream" servisining quvvatini oshirish kifoya.

3. Xatolarga chidamlilik: Tizim "yashab qolish" qobiliyati yuqori.

### Kamchiliklari (-):

1. Murakkablik: Servislar orasidagi aloqani (API, Messaging) sozlash ko'p vaqt va tajriba talab qiladi.

2. Narx: Har bir servisni alohida infratuzilmada saqlash server xarajatlarini oshiradi.

3. Monitoring qiyinligi: Qaysi servisda xato bo'layotganini kuzatish uchun maxsus monitoring vositalari kerak.

### Qaysi biri yaxshiroq va nima uchun?

2-variant (Mikroservis) ancha yaxshiroq.

Nima uchun? Imtihon — bu vaqt bilan chegaralangan va stressli jarayon. Agar tizim monolit bo'lsa va video tahlil moduli ortiqcha yuklama tufayli "qulasa", 5000 nafar talabaning imtihoni to'xtab qoladi. Mikroservisda esa hatto AI moduli to'xtab qolsa ham, video yozib olinishda davom etadi va talaba imtihonini tugatib qo'ya oladi. Bu biznes va foydalanuvchi ishonchi nuqtayi nazaridan eng to'g'ri yo'ldir.