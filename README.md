# AirGuard ID — Prediksi Kualitas Udara Indonesia

Aplikasi web statis untuk memantau kualitas udara dan ISPU di seluruh Indonesia.
Data diambil langsung dari [Open-Meteo Air Quality & Geocoding API](https://open-meteo.com/)
di browser, sehingga **tidak memerlukan backend, database, atau environment variable**.

## Fitur

- Pencarian kota/kabupaten di Indonesia via geocoding Open-Meteo.
- Data ISPU, PM2.5, PM10, NO₂, CO, O₃, SO₂, dan US AQI.
- Grafik tren interaktif dengan rentang 24/48/72 jam.
- Prakiraan 5 hari dan ekspor CSV.
- Favorit, riwayat lokasi, tema terang/gelap (disimpan di `localStorage`).
- Tampilan responsif untuk desktop dan mobile.

## Struktur Proyek

```
.
├── index.html                 # Seluruh logika aplikasi (HTML + JS)
├── assets/tailwind.css        # CSS Tailwind hasil build (sudah di-commit)
├── src/input.css              # Sumber direktif Tailwind
├── tailwind.config.js         # Konfigurasi Tailwind (dark mode, font)
├── favicon.svg                # Ikon situs
├── vercel.json                # Redirect, clean URLs, dan security headers
├── .lighthouserc.json         # Konfigurasi Lighthouse CI
├── .github/workflows/         # CI mobile responsiveness
├── about.md / alur.md         # Dokumen rancangan arsitektur (aspirational)
└── kekurangan.md / error_test.md
```

> Catatan: `about.md` dan `alur.md` menjelaskan rancangan arsitektur lengkap
> (backend, database, Docker). Implementasi yang ada saat ini adalah **frontend
> statis satu file** yang siap di-hosting di Vercel.

## Menjalankan Secara Lokal

Karena berupa file statis, cukup jalankan web server sederhana dari root proyek:

```bash
python3 -m http.server 8000
```

Lalu buka <http://localhost:8000>. Jangan membuka `index.html` langsung dengan
`file://` karena beberapa browser membatasi `localStorage` dan request API.

Alternatif:

```bash
npm run dev
```

### Jika Mengubah Class Tailwind

CSS Tailwind sudah dikompilasi dan disimpan di `assets/tailwind.css`, jadi
deployment tidak membutuhkan langkah build. Setelah menambah/mengubah class di
`index.html`, build ulang CSS:

```bash
npm install
npm run build:css
```

Atau jalankan mode watch saat mengembangkan:

```bash
npm run watch:css
```

## Deploy ke GitHub

Repositori sudah terhubung ke:

```text
https://github.com/nicholasdewidjaa-sudo/air_quality.git
```

Simpan perubahan dan push:

```bash
git add .
git commit -m "Prepare static site for Vercel deployment"
git push origin main
```

Jika ingin memakai repositori baru, buat repo kosong di GitHub lalu:

```bash
git remote set-url origin https://github.com/<username>/<repo>.git
git push -u origin main
```

## Deploy ke Vercel

### Opsi A — Dashboard (paling mudah, tanpa CLI)

1. Buka <https://vercel.com/new> dan login dengan akun GitHub.
2. Pilih repositori `air_quality`, lalu klik **Import**.
3. Isi konfigurasi berikut:
   - **Framework Preset**: `Other`
   - **Root Directory**: `./`
   - **Build Command**: kosongkan (CSS sudah di-commit)
   - **Output Directory**: `.` (titik)
   - **Install Command**: biarkan default (`npm install`)
4. Klik **Deploy**. Situs akan tersedia di
   `https://<nama-proyek>.vercel.app`.
5. Setiap `git push` ke branch `main` akan otomatis memicu deployment baru.
   Pull request juga mendapat preview URL sendiri.

### Opsi B — Vercel CLI

```bash
npm install -g vercel
vercel login
vercel --prod
```

Saat diminta konfigurasi, pilih:

- Set up and deploy: **yes**
- Link to existing project: **no**
- Project name: `air-quality` (atau nama lain)
- Directory: `./`
- Override settings: **no**

### Setelah Deploy

- **Custom domain**: buka Project → Settings → Domains, lalu tambahkan domain
  dan atur DNS sesuai instruksi Vercel.
- **Rollback**: buka tab Deployments, pilih deployment lama, klik **Promote**.
- **Preview**: setiap PR otomatis mendapat URL preview.

## Deploy ke GitHub Pages (Opsional)

Repositori sudah memiliki workflow `.github/workflows/static.yml` untuk
GitHub Pages. Jika ingin mengaktifkannya:

1. Buka **Settings → Pages** di repositori GitHub.
2. Pada **Build and deployment → Source**, pilih **GitHub Actions**.
3. Push ke branch `main` (atau jalankan workflow secara manual dari tab
   **Actions**).
4. Situs tersedia di `https://<username>.github.io/air_quality/`.

Vercel tetap direkomendasikan untuk deployment utama karena preview per PR,
rollback, dan pengaturan domain lebih mudah.

## CI — Mobile Responsiveness

Workflow `.github/workflows/mobile_responsive.yml`:

1. Menginstal dependency dan memverifikasi `assets/tailwind.css` sudah sesuai
   dengan `index.html` (gagal jika lupa build ulang).
2. Menjalankan Lighthouse CI dengan emulasi mobile setiap ada perubahan
   HTML/CSS/JS.

Skor terakhir di lingkungan pengujian:

| Kategori | Skor |
| --- | ---: |
| Performance | 95 |
| Accessibility | 100 |
| Best Practices | 100 |
| SEO | 100 |

Assertion diset ke `warn` agar hasil yang sedikit di bawah ambang tidak
memblokir deployment.

Untuk menjalankannya secara lokal:

```bash
CHROME_PATH=/usr/bin/chromium npx --yes @lhci/cli@0.16.1 autorun
```

## Catatan Teknis

- **Sumber data**: Open-Meteo (gratis, tanpa API key). Koneksi internet
  diperlukan; jika API gagal, aplikasi menampilkan data terakhir dan tombol
  "Coba lagi".
- **Penyimpanan**: tema, favorit, dan riwayat hanya tersimpan di browser
  pengguna (`localStorage`), bukan di server.
- **Tailwind CSS** dikompilasi menjadi `assets/tailwind.css` (26 KB) sehingga
  tidak ada lagi CDN Tailwind dan tidak ada warning produksi. Chart.js dan
  Phosphor Icons masih dimuat dari CDN dengan versi yang dikunci.
- **Keamanan**: `vercel.json` menambahkan header `X-Content-Type-Options`,
  `X-Frame-Options`, `Referrer-Policy`, dan `Permissions-Policy`.
- **Redirect**: URL lama
  `/aplikasi_prediksi_kualitas_udara_indonesia.html` diarahkan permanen ke `/`.

## Troubleshooting

| Masalah | Penyebab & solusi |
| --- | --- |
| Halaman 404 di Vercel | Pastikan file utama bernama `index.html` dan Output Directory `.`. |
| Data tidak muncul | Open-Meteo diblokir jaringan/adblock, atau sedang down. Cek tab Network browser. |
| Tampilan tidak berubah setelah edit | Jalankan `npm run build:css`, lalu commit `assets/tailwind.css`. |
| CI gagal di langkah "Verify Tailwind CSS" | Build ulang CSS secara lokal dan push hasilnya. |
| `npm ci` gagal | Pastikan `package-lock.json` ikut ter-commit. |
