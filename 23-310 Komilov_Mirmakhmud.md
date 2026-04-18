Car Wash Scheduling App — Tizim Talablari va Arxitekturasi

Loyiha mavzusi: Avtomobil yuvish xizmatini bron qilish ilovasi


 1-bosqich: 

 Funksional talablar (Functional Requirements)


Bron qilish (Booking) — Foydalanuvchi qulay vaqt, xizmat turi va avtomobil ma'lumotlarini kiritib, car wash uchun online bron yaratishi mumkin. 
Xizmat turlari katalogi — Tizim mavjud xizmat turlarini (Express, Standard, Premium) narxi va davomiyligi bilan ko'rsatishi kerak. 
Real-vaqt jadval — Foydalanuvchi va operator bandlik jadvalni real vaqtda ko'rishi, bo'sh slotlarni aniqlashi mumkin. 
Bildirishnomalar (Notifications)** — Tizim bron tasdiqlanganda, eslatma vaqtida va xizmat tugaganda SMS/email/push bildirish yuborishi kerak. 
Hisobot va tarix — Operator admin panelda kunlik/haftalik xizmat statistikasini, daromadni ko'rishi va PDF hisobot olishi mumkin. 


 Nofunksional talablar (Non-Functional Requirements)


Ishlash tezligi (Performance) — Sahifalar 2 soniyadan kam vaqtda yuklanishi kerak; bron so'rovi 1 soniyada qayta ishlanishi lozim. 
Xavfsizlik (Security) — Foydalanuvchi ma'lumotlari shifrlangan (HTTPS/TLS), parollar hashing (bcrypt) orqali saqlanishi shart. 
Ishonchlilik (Reliability) — Tizim uptime 99.5% bo'lishi kerak; server xatosi bo'lsa, ma'lumotlar yo'qolmasligi ta'minlanishi lozim. 
Kengayuvchanlik (Scalability) — Bir vaqtda kamida 500 ta faol foydalanuvchini ko'tara olishi, yuklama oshganda gorizontal scale qilish imkoniyati bo'lishi kerak. 
Foydalanish qulayligi (Usability) — Interfeys mobil qurilmalarga moslashtirilgan (Responsive Design), minimal texnik bilim talab etmagan holda 3 bosqichda bron yaratish mumkin bo'lishi kerak.


 2-bosqich. Arxitektura


 Variant 1 — Monolithic Architecture
https://viewer.diagrams.net/?tags=%7B%7D&lightbox=1&highlight=0000ff&edit=_blank&layers=1&nav=1&title=%D0%94%D0%B8%D0%B0%D0%B3%D1%80%D0%B0%D0%BC%D0%BC%D0%B0%20%D0%B1%D0%B5%D0%B7%20%D0%BD%D0%B0%D0%B7%D0%B2%D0%B0%D0%BD%D0%B8%D1%8F.drawio&dark=auto#Uhttps%3A%2F%2Fdrive.google.com%2Fuc%3Fid%3D1RWWFqa7hKnYWwzxKlxh_wqzbYNLzO7t1%26export%3Ddownload

Afzalliklari
 Ishlab chiqish va deploy qilish oddiy — bitta server, bitta kod bazasi
Debugging va testing oson
 Kichik jamoa uchun mos (1–3 dasturchi)
 Kam xarajat (bitta server kifoya)

Kamchiliklari
Loyiha kattalashganda kod bazasi murakkablashadi
Bitta modul xatosi butun tizimni to'xtatishi mumkin
Alohida modullarni mustaqil scale qilib bo'lmaydi
Texnologiyani o'zgartirish qiyin

Variant 2 — Mikroservislar Arxitekturasi (Microservices Architecture)

https://drive.google.com/file/d/1RWWFqa7hKnYWwzxKlxh_wqzbYNLzO7t1/view?usp=sharing


Afzalliklari
- Har bir servis mustaqil deploy va scale qilinadi
- Bitta servis xatosi boshqalarga ta'sir qilmaydi
- Turli xizmatlar uchun turli texnologiya ishlatish mumkin
- Katta jamoalar parallel ishlashi mumkin

Kamchiliklari
- Infratuzilma murakkab — DevOps bilimi talab etiladi
- Servislar orasidagi muloqot (network latency) qo'shimcha vaqt oladi
- Monitoring va debugging qiyinlashadi
- Boshlang'ich xarajat va sozlash vaqti ko'p


Xulosa:

Car Wash Scheduling App uchun — Variant 1 (Monolitik Arxitektura) yaxshiroq.

Sabablari:

1. **Loyiha ko'lami kichik** — Bitta car wash yoki kichik tarmoq uchun mo'ljallangan ilova juda katta infratuzilma talab qilmaydi.
2. **Tez ishlab chiqish** — Monolitik yondashuv bilan MVP (Minimum Viable Product) tezroq tayyorlanadi va mijozga yetkaziladi.
3. **Kam xarajat** — Bitta server va bitta ma'lumotlar bazasi bilan boshlanishi mumkin.
4. **Kelajakda o'tish mumkin** — Loyiha o'sganda (masalan, 50+ filial) kodning alohida qismlarini mikroservislarga ko'chirish mumkin.

Mikroservislar faqat tizim juda kengaygan, bir vaqtda yuz minglab foydalanuvchi bo'lganda va katta DevOps jamoasi mavjud bo'lganda maqsadga muvofiq bo'ladi.


