# Bansos Media Monitor

Dashboard monitoring berita bansos Indonesia secara real-time dengan analisis sentimen AI.

---

## 🚀 Setup & Deploy (15-20 menit)

### 1. Persiapan akun (semua gratis)

| Layanan | Link | Kegunaan |
|---------|------|----------|
| Supabase | https://supabase.com | Database PostgreSQL |
| Vercel | https://vercel.com | Hosting website |
| GitHub | https://github.com | Repo + scheduler |
| NewsAPI | https://newsapi.org | Sumber berita |
| Anthropic | https://console.anthropic.com | AI analisis sentimen |

---

### 2. Setup Supabase Database

1. Buat project baru di https://supabase.com
2. Pergi ke **SQL Editor**
3. Copy-paste isi file `lib/supabase-schema.sql` lalu klik **Run**
4. Pergi ke **Settings → API** dan catat:
   - `Project URL` → `NEXT_PUBLIC_SUPABASE_URL`
   - `anon public` key → `NEXT_PUBLIC_SUPABASE_ANON_KEY`
   - `service_role` key → `SUPABASE_SERVICE_ROLE_KEY`

---

### 3. Setup Environment Variables

Copy file `.env.example` menjadi `.env.local` lalu isi semua nilai:

```bash
cp .env.example .env.local
```

```env
NEXT_PUBLIC_SUPABASE_URL=https://xxxx.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=eyJhbG...
SUPABASE_SERVICE_ROLE_KEY=eyJhbG...

ANTHROPIC_API_KEY=sk-ant-...

NEWSAPI_KEY=xxxxxxxxxxxxxxxx   # dari https://newsapi.org/register

CRON_SECRET=buat-string-acak-panjang-di-sini
```

---

### 4. Deploy ke Vercel

```bash
# Install Vercel CLI
npm i -g vercel

# Login & deploy
vercel login
vercel --prod
```

Saat diminta, masukkan semua environment variable dari `.env.local`.

Atau lewat GitHub:
1. Push repo ke GitHub
2. Import project di https://vercel.com/new
3. Tambahkan semua env vars di Vercel dashboard → Settings → Environment Variables

---

### 5. Setup GitHub Actions (Scheduler Otomatis)

1. Push repo ke GitHub
2. Pergi ke repo → **Settings → Secrets and variables → Actions**
3. Tambahkan secrets:
   - `SITE_URL` → URL Vercel kamu (contoh: `https://bansos-monitor.vercel.app`)
   - `CRON_SECRET` → nilai yang sama dengan di `.env.local`

GitHub Actions akan otomatis:
- Fetch berita setiap jam **06:00 WIB**
- Update setiap **2 jam** sampai jam **22:00 WIB**
- Berhenti otomatis di luar jam operasional

---

### 6. Test Manual

```bash
# Jalankan development server
npm run dev

# Test fetch berita secara manual (terminal lain)
SITE_URL=http://localhost:3000 CRON_SECRET=dev-secret FORCE=true node scripts/fetch-news.js
```

---

## 📁 Struktur Project

```
bansos-monitor/
├── app/
│   ├── api/
│   │   ├── fetch-news/route.js   # Endpoint fetch + simpan berita
│   │   ├── articles/route.js     # API ambil artikel dari DB
│   │   └── sentiment/route.js    # AI analisis on-demand
│   ├── dashboard/page.js         # Halaman utama dashboard
│   ├── layout.js
│   └── globals.css
├── lib/
│   ├── supabase.js               # Supabase client
│   ├── supabase-schema.sql       # Schema database (jalankan 1x)
│   ├── news-fetcher.js           # Google News RSS + NewsAPI
│   └── sentiment-analyzer.js    # Claude AI batch analysis
├── scripts/
│   └── fetch-news.js             # Script manual trigger
├── .github/
│   └── workflows/
│       └── fetch-news.yml        # Cron job GitHub Actions
├── .env.example                  # Template environment variables
└── README.md
```

---

## 🔧 Konfigurasi Tambahan

### Tambah kata kunci pencarian
Edit `lib/news-fetcher.js` → array `BANSOS_KEYWORDS` dan `queries`.

### Ubah jam operasional
Edit `.github/workflows/fetch-news.yml` → bagian `cron` dan kondisi `WIB_HOUR`.

### Tambah sumber RSS
Edit `lib/news-fetcher.js` → fungsi `fetchGoogleNews()`, tambahkan query baru.

---

## 💰 Estimasi Biaya

| Layanan | Tier Gratis | Keterangan |
|---------|-------------|------------|
| Vercel | ✅ Gratis | 100GB bandwidth/bulan |
| Supabase | ✅ Gratis | 500MB database, 2GB transfer |
| GitHub Actions | ✅ Gratis | 2000 menit/bulan |
| NewsAPI | ✅ Gratis | 100 request/hari (developer) |
| Claude API | 💳 ~$1-3/bulan | Haiku model, sangat murah |

Total: **gratis** kecuali Claude API ~Rp15.000-50.000/bulan
