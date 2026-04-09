# EPT & EPB Monitor — Setup Guide

Dashboard monitoring EPT/EPB berbasis Google Sheets, deploy di Vercel (gratis).

---

## Prasyarat
- Akun Google (untuk Google Cloud Console & Sheets)
- Akun Vercel (gratis) — daftar di vercel.com
- Akun GitHub (untuk deploy via Vercel)

---

## Langkah 1 — Buat Google Sheets API Key

1. Buka https://console.cloud.google.com
2. Buat project baru (atau pakai yang sudah ada)
3. Klik **APIs & Services → Library**
4. Cari **Google Sheets API** → Enable
5. Klik **APIs & Services → Credentials → Create Credentials → API Key**
6. Copy API key yang muncul
7. (Opsional tapi disarankan) Klik **Edit API Key** → di bagian *API restrictions*, pilih **Restrict key** → centang **Google Sheets API** saja

---

## Langkah 2 — Set Google Spreadsheet ke Public

1. Buka spreadsheet: https://docs.google.com/spreadsheets/d/1cRoZyleTMYqBhZmzr9OgQVRz29ZAyGYeTm_aieKAg3g
2. Klik **Share** → **Change to anyone with the link** → pilih **Viewer**
3. Klik Done

---

## Langkah 3 — Isi API Key di index.html

Buka `index.html`, cari baris:

```js
const API_KEY = 'YOUR_GOOGLE_SHEETS_API_KEY'; // ← ganti ini
```

Ganti dengan API key dari Langkah 1:

```js
const API_KEY = 'AIzaSy...'; // API key kamu
```

---

## Langkah 4 — Deploy ke Vercel

### Cara A: Via GitHub (disarankan)

1. Upload folder `ept-dashboard` ke repository GitHub baru
2. Buka https://vercel.com → New Project
3. Import repository tersebut
4. Klik Deploy — selesai!

URL dashboard akan seperti: `https://ept-dashboard-xxx.vercel.app`

### Cara B: Via Vercel CLI

```bash
npm i -g vercel
cd ept-dashboard
vercel
```

---

## Struktur File

```
ept-dashboard/
├── index.html     # Dashboard utama (semua dalam 1 file)
└── vercel.json    # Config Vercel static deploy
```

---

## Konfigurasi di index.html

Semua konfigurasi ada di bagian `CONFIG` di dalam `<script>`:

```js
const SS_ID   = '1cRoZyleTMYqBhZmzr9OgQVRz29ZAyGYeTm_aieKAg3g'; // ID Spreadsheet
const API_KEY = 'YOUR_GOOGLE_SHEETS_API_KEY';                      // API Key Google
const R_EPT   = 'EPT!A:K';   // Range sheet EPT
const R_EPB   = 'EPB!A:I';   // Range sheet EPB
```

---

## Cara Kerja

```
Browser → Google Sheets API (https) → Parse data → Render chart + tabel
```

Tidak ada server, tidak ada backend — semua proses di browser user.
Data di-cache di memory selama sesi. Klik **Refresh** untuk ambil data terbaru.

---

## Migrasi ke BigQuery (nanti)

Ketika data sudah pindah ke BigQuery, ganti fungsi `fetchSh()` dengan
fetch ke BigQuery REST API atau gunakan Vercel Serverless Function
sebagai proxy. Tampilan dashboard tidak perlu diubah.

---

## Troubleshooting

| Error | Penyebab | Solusi |
|-------|----------|--------|
| `HTTP 403` | API Key salah / Sheets tidak public | Cek Langkah 1 & 2 |
| `HTTP 400` | Range nama sheet salah | Pastikan nama sheet di Spreadsheet sesuai config |
| Data kosong | Header kolom tidak cocok | Cek nama kolom di Sheet vs yang diharapkan parser |
| CORS error | — | Tidak terjadi di Sheets API v4 (sudah CORS-enabled) |
