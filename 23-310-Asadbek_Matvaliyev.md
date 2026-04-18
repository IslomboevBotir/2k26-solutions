# Expense Sharing Application backend arxitekturasi

## 1. Loyiha mavzusi

**Loyiha nomi:** Expense Sharing Application  

**Tavsif:**  
Bu tizim foydalanuvchilarga umumiy xarajatlarni boshqarish, guruh ichida pulni bo‘lish, kim kimga qancha qarz ekanini hisoblash va qarzlarni yopish imkonini beradi.

---

# 1-bosqich. Talablar

## 1.1. Funksional talablar

1. Foydalanuvchi ro‘yxatdan o‘tishi va tizimga kirishi kerak  
2. Foydalanuvchi guruh yaratishi va boshqa foydalanuvchilarni qo‘shishi kerak  
3. Foydalanuvchi xarajat qo‘sha olishi kerak (summa, kim to‘ladi, kimlar orasida bo‘linadi)  
4. Tizim qarz va balansni avtomatik hisoblab chiqishi kerak  
5. Foydalanuvchi qarzni yopish (settlement) qilishi kerak  

---

## 1.2. Nofunksional talablar

1. Tizim xavfsiz bo‘lishi kerak (parollar shifrlanadi)  
2. Tizim bir vaqtning o‘zida ko‘p foydalanuvchini qo‘llab-quvvatlashi kerak  
3. Tizim tez ishlashi kerak  
4. Tizim kengaytiriladigan bo‘lishi kerak  
5. Tizim barqaror ishlashi kerak  

---

# 2-bosqich. Arxitektura

## 2.1. 1-variant: Monolit arxitektura

### Tavsif
Barcha modullar bitta backend ichida ishlaydi.

### Tizim sxemasi

```text
[ Mobile App ]
      |
      v
  [ Backend ]
  - Auth
  - Users
  - Groups
  - Expenses
  - Balance
  - Settlement
      |
      v
   [ Database ]
