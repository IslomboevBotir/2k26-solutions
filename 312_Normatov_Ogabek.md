Mavzu: Tizim talablari va arxitekturasi

Loyiha mavzusi:Public Transport Route Helper

1-bosqich. Funksional talablar

Foydalanuvchi boshlanish va manzil nuqtasini kiritishi kerak
Tizim optimal marshrutni (eng tez yoki eng qisqa) hisoblab berishi kerak
Turli transport turlarini (avtobus, metro va boshqalar) qo‘llab-quvvatlashi kerak
Yo‘nalish bo‘yicha bekatlar va transfer nuqtalarini ko‘rsatishi kerak
Marshrut xaritada vizual ko‘rinishda chiqarilishi kerak

Nofunksional talablar
Tizim javob vaqti 5 soniyadan oshmasligi kerak
Tizim yuqori yuklamaga bardosh bera olishi kerak
Tizim xavfsiz bo‘lishi kerak (ma’lumotlar himoyasi)
Tizim mobil va web platformalarda ishlashi kerak
Tizim kengaytiriladigan (scalable) bo‘lishi kerak

2-bosqich. Arxitektura

Variant 1: Monolit arxitektura
Tizim sxemasi: Frontend → Backend → Database

Afzalliklari:Oddiy va tez ishlab chiqiladi, Oson deploy qilinadi, Kichik loyihalar uchun qulay
Kamchiliklari:Kengaytirish qiyin, Katta yuklamada sekinlashadi, Bitta joydagi xato butun tizimga ta’sir qiladi

Variant 2: Mikroservis arxitekturasi
Tizim sxemasi:Frontend → API Gateway → Routing Service → Database
                        → Auth Service
                        → Geo Service

Afzalliklari: Mustaqil servislar orqali scaling oson,Yuqori yuklamaga bardoshli, Har bir modulni alohida rivojlantirish mumkin

Kamchiliklari:Murakkab arxitektura, Deploy va boshqarish qiyinroq, Ko‘proq resurs talab qiladi