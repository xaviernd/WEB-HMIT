# Website Himpunan (mirip hmit-its.com) — Laravel

Paket ini berisi **kode aplikasi kustom** (Models, Controllers, Migrations, Views, Routes, Seeder)
untuk sebuah website himpunan mahasiswa lengkap dengan:

- **Beranda** — hero, berita terbaru, preview galeri
- **Tentang** — sejarah, visi & misi (bisa diedit dari admin)
- **Struktur Organisasi** — daftar pengurus per departemen, dengan foto
- **Berita** — daftar + detail berita, pencarian, filter kategori, hitung jumlah dilihat
- **Galeri** — dokumentasi kegiatan (upload banyak foto sekaligus)
- **Kontak** — form kontak (kirim email ke pengurus)
- **Panel Admin** (login) — CRUD Berita (dengan upload gambar), Kategori, Galeri,
  Pengurus/Struktur Organisasi, dan Edit halaman Profil/Tentang

> Catatan: `hmit-its.com` adalah website React/JS (SPA) sehingga isi kontennya tidak bisa
> saya salin persis. Struktur & fitur di bawah ini dibuat menyerupai website himpunan pada
> umumnya (Beranda, Tentang, Struktur, Berita, Galeri, Kontak) dan bisa Anda sesuaikan
> teks/warna/logo-nya lewat panel admin maupun langsung di file Blade.

---

## 1. Persiapan (butuh PHP 8.2+, Composer, dan database MySQL/MariaDB)

Karena environment saya tidak punya akses ke Packagist, saya tidak bisa menjalankan
`composer install` di sini. Silakan jalankan langkah berikut **di komputer/server Anda**:

```bash
# 1. Buat proyek Laravel baru (kosong)
composer create-project laravel/laravel hmit-website "^11.0"
cd hmit-website

# 2. Salin/timpa seluruh folder dari paket ini (app, database, resources, routes, public/images)
#    ke dalam folder hmit-website yang baru dibuat. Contoh (Linux/Mac):
cp -r ../paket-hmit-website/app/* app/
cp -r ../paket-hmit-website/database/* database/
cp -r ../paket-hmit-website/resources/views/* resources/views/
cp -r ../paket-hmit-website/routes/web.php routes/web.php
cp -r ../paket-hmit-website/public/images public/

# 3. Install dependency & siapkan .env
composer install
cp .env.example .env
php artisan key:generate
```

## 2. Konfigurasi Database

Edit file `.env`, sesuaikan dengan database Anda:

```
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=hmit_website
DB_USERNAME=root
DB_PASSWORD=
```

Buat database `hmit_website` lalu jalankan migrasi + seeder (contoh data awal):

```bash
php artisan migrate --seed
```

Seeder ini akan membuat:
- Akun admin login: **admin@hmit-its.com** / **password123** (⚠️ segera ganti setelah login pertama)
- 4 kategori berita contoh (Pengumuman, Kegiatan, Prestasi, Kaderisasi)
- Konten "Tentang" default (bisa langsung diedit lewat admin)

## 3. Hubungkan storage untuk upload gambar

Laravel menyimpan file upload (berita, galeri, foto pengurus, logo) di `storage/app/public`.
Agar bisa diakses lewat browser, buat symlink:

```bash
php artisan storage:link
```

## 4. Jalankan secara lokal

```bash
php artisan serve
```

Buka:
- Website publik: `http://127.0.0.1:8000`
- Panel admin: `http://127.0.0.1:8000/admin/login`

## 5. Deploy ke hosting/VPS

1. Upload seluruh proyek ke server (folder root domain harus mengarah ke folder `public/`).
2. Jalankan `composer install --optimize-autoloader --no-dev` di server.
3. Set `.env` sesuai kredensial database & domain produksi (`APP_ENV=production`, `APP_DEBUG=false`).
4. `php artisan key:generate` (jika belum ada key), `php artisan migrate --seed`, `php artisan storage:link`.
5. Set permission folder `storage` dan `bootstrap/cache` agar bisa ditulis web server (`chmod -R 775` + kepemilikan user web server).
6. Jalankan `php artisan config:cache`, `php artisan route:cache`, `php artisan view:cache` untuk performa produksi.
7. Untuk kirim email dari form Kontak, isi variabel `MAIL_*` di `.env` (mis. pakai SMTP Gmail atau layanan seperti Mailgun/Resend).

Cocok untuk hosting cPanel (shared hosting Laravel-ready) maupun VPS dengan Nginx/Apache + PHP-FPM.

---

## Struktur Fitur & Halaman

| Halaman Publik        | Route                  |
|------------------------|-------------------------|
| Beranda                 | `/`                     |
| Tentang                 | `/tentang`               |
| Struktur Organisasi     | `/struktur-organisasi`   |
| Berita (daftar)         | `/berita`                |
| Berita (detail)         | `/berita/{slug}`         |
| Galeri                  | `/galeri`                |
| Kontak                  | `/kontak`                |

| Panel Admin (butuh login) | Route              |
|-----------------------------|----------------------|
| Login                        | `/admin/login`       |
| Dashboard                    | `/admin`             |
| CRUD Berita                  | `/admin/berita`      |
| Kategori Berita              | `/admin/kategori`    |
| Galeri (upload/hapus)        | `/admin/galeri`      |
| Struktur Organisasi (CRUD)   | `/admin/pengurus`    |
| Edit Profil/Tentang Himpunan | `/admin/profil`      |

## Catatan Teknis

- Frontend menggunakan **Tailwind CSS via CDN** + **Alpine.js via CDN** — tidak butuh Node/npm build,
  jadi lebih mudah untuk shared hosting. Bila ingin build Tailwind lokal (lebih optimal untuk produksi),
  Anda bisa migrasikan ke setup Vite bawaan Laravel.
- Semua gambar upload (berita/galeri/pengurus/logo) disimpan di `storage/app/public/...`
  dan diakses lewat `storage:link`.
- Struktur database: tabel `beritas`, `kategoris`, `galeris`, `penguruses`, `profils`, plus
  tabel bawaan Laravel (`users`, dll).
- Silakan ganti nama, logo, warna (`primary`/`accent` di `tailwind.config` pada layout),
  dan teks di halaman "Tentang" lewat panel admin agar sesuai identitas himpunan Anda.

Selamat mengembangkan! Jika ingin saya tambahkan fitur lain (misalnya event/agenda kegiatan,
komentar berita, multi-admin dengan role, atau API untuk aplikasi mobile), tinggal bilang saja.
