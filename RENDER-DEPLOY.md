# Karol on Render — راهنمای نصب

## مرحله ۱: ساخت GitHub Repository

1. به **GitHub.com** برو و یه اکانت بساز (اگه نداری).
2. یه repository جدید بساز به اسم `karol` (private یا public فرقی نداره).
3. فایل‌های پوشه‌ی `karol-render` رو تو repository آپلود کن:
   - می‌تونی فایل‌ها رو zip کنی و با drag-and-drop تو GitHub بندازی
   - یا اگه Git بلدی: `git init && git add . && git commit -m "karol" && git push`

## مرحله ۲: ساخت Web Service روی Render

1. به **Render.com** برو و با GitHub وارد شو.
2. **New +** → **Web Service**.
3. repository کارول رو انتخاب کن.
4. تنظیمات:

| فیلد | مقدار |
|------|-------|
| **Name** | `karol` |
| **Runtime** | `Node` |
| **Build Command** | `npm install && npm run build` |
| **Start Command** | `npm start` |
| **Instance Type** | `Free` |

5. **Advanced** → **Environment Variables**:

| کلید | مقدار |
|------|-------|
| `DATABASE_URL` | (از مرحله ۳ می‌گیریم) |
| `AUTH_SECRET` | `a7b3c9f2e8d1k4m6n3p8q2r5s7t0u3w6` |
| `STORAGE_ROOT` | `/var/data/storage` |
| `STORAGE_PUBLIC_BASE` | `/storage` |
| `NODE_ENV` | `production` |

6. **Create Web Service** بزن.

## مرحله ۳: ساخت PostgreSQL Database

1. تو Render → **New +** → **PostgreSQL**.
2. تنظیمات:

| فیلد | مقدار |
|------|-------|
| **Name** | `karol-db` |
| **Database** | `karol` |
| **User** | `karol` |
| **Region** | همون region که web service |
| **Instance Type** | `Free` |

3. **Create Database** بزن.
4. بعد از ساخت، روی database بزن و **Internal Database URL** رو کپی کن.
5. برگرده به web service → **Environment** → `DATABASE_URL` رو با URL که کپی کردی set کن.

## مرحله ۴: ساخت Disk (برای آپلود فایل)

1. تو web service → **Disks** → **Add Disk**.
2. تنظیمات:

| فیلد | مقدار |
|------|-------|
| **Name** | `storage` |
| **Mount Path** | `/var/data/storage` |
| **Size** | `1 GB` |

3. **Save** بزن.

## مرحله ۵: Manual Deploy + Seed

1. تو web service → **Manual Deploy** → **Clear build cache & deploy**.
2. صبر کن تا build تموم بشه (۵-۱۰ دقیقه).
3. بعد از deploy موفق، به **Shell** برو (تو web service).
4. این دستورات رو بزن:

```bash
npx prisma db push
npx tsx scripts/seed.ts
```

## مرحله ۶: تست

به آدرسی که Render داده برو (مثل `https://karol-xxxx.onrender.com`).

- **نام کاربری**: `admin`
- **رمز عبور**: `admin123`

## نکته‌ها

- Plan رایگان Render بعد از ۱۵ دقیقه inactive، سایت رو خواب می‌کنه. اولین درخواست بعد از خواب، ۳۰ ثانیه طول می‌کشه.
- اگه می‌خوای همیشه روشن باشه، plan Starter ($7/ماه) بگیر.
- چت/ویس/تماشای گروهی نیاز به سرویس socket.io جدا داره — می‌تونم بعداً اضافه کنم.
