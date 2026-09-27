# 📚 Smart Library Management System

Aplikasi perpustakaan digital lengkap yang dibangun **sesuai 100% workflow dokumen** *"Workflow Smart Library Management System"* — Laravel 12, PHP 8.2+, PWA installable, offline-aware, WhatsApp notification engine, barcode/QR scanner, REST API, plus **landing page SaaS dengan konten yang bisa dikelola admin**.

---

## 🚀 Cara Menjalankan

```bash
cd smart-library

# 1. Dependensi (sudah ter-install)
composer install
npm install && npm run build

# 2. Konfigurasi
cp .env.example .env
php artisan key:generate

# 3. Database (default: SQLite — langsung jalan tanpa setup)
touch database/database.sqlite
php artisan migrate:fresh --seed

# 4. Jalankan
php artisan serve
# → http://127.0.0.1:8000
```



### 🗄️ Ganti ke MySQL/MariaDB (production)

`.env`:
```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=smart_library
DB_USERNAME=root
DB_PASSWORD=
```
Lalu `php artisan migrate:fresh --seed`.

### ⏰ Scheduler & Queue (WAJIB untuk reminder otomatis)

```bash
# Cron sekali saja di server:
* * * * * cd /path/to/smart-library && php artisan schedule:run >> /dev/null 2>&1
```

Scheduler yang terdaftar (`bootstrap/app.php`):
- **07:00 harian** → `library:reminders` — reminder WhatsApp H-7/H-3/H-1/Hari-H/H+1/H+3
- **23:00 harian** → `library:overdue` — tandai terlambat, hitung denda otomatis, ekspirasi reservasi
- **setiap menit** → worker antrian WhatsApp (`queue:work`)

Manual (demo): `php artisan library:reminders` dan `php artisan library:overdue`.

---

## ✅ Cakupan Fitur (per Section Dokumen)

| # | Section Dokumen | Implementasi |
|---|-----------------|--------------|
| 1 | Arsitektur Besar | Laravel backend + Blade/Tailwind/Alpine + MySQL/SQLite + Queue + WhatsApp API |
| 2 | Role User | `super_admin`, `librarian`, `member`, guest — middleware `role:` |
| 3 | Authentication | Login + **CAPTCHA matemika** + rate limiting + remember me + forgot/reset password + last login/IP + sesi |
| 4 | Dashboard Member | Sambutan, search, 4 statistik (dipinjam/terlambat/favorit/denda), jatuh tempo + tombol Perpanjang, rekomendasi |
| 5 | Katalog Buku | 16 field lengkap + 6 status (available/borrowed/reserved/damaged/lost/maintenance) |
| 6 | Barcode Scanner | 📷 Kamera via html5-qrcode (pinjam/kembali/self-service/inventaris) + fallback input manual |
| 7 | Peminjaman | 5 validasi berurutan (member aktif→kuota→denda→stok→reserved) → loan #LIB-YYYY-000001 → WA konfirmasi |
| 8 | Pengembalian | Scan → cari active loan → hitung denda otomatis → update stok → struk → WA |
| 9 | Smart Reminder | Scheduler harian H-7/H-3/H-1/H/H+1/H+3 via queue WhatsApp, on/off per jadwal di pengaturan |
| 10 | WhatsApp Engine | Template editable (13 template), message queue, history, failed/retry, test kirim, gateway URL opsional (sandbox default) |
| 11 | Reservasi | Antrean per buku, notifikasi "siap diambil", deadline ambil → expired → antrean berikutnya |
| 12 | Perpanjangan | Validasi reservasi lain + kuota extend; hari & kuota configurable |
| 13 | Smart Search | Judul/subjudul/ISBN/keyword/penulis/penerbit + 7 filter + **rekomendasi berbasis riwayat & favorit** |
| 14 | Dashboard Admin | 10 statistik + chart 12 bulan + buku terpopuler + terlambat + aktivitas terbaru |
| 15 | Dashboard Petugas | Aksi cepat pinjam/kembali + transaksi terbaru |
| 16 | Master Data | Kategori, penulis, penerbit, rak (ruang/lokasi), tipe member |
| 17 | Multi Copy | `book_copies` per eksemplar dengan barcode unik `{ISBN}-{copyNo}` + barcode image (JsBarcode) |
| 18 | QR Code | QR detail buku, QR kartu member (`SMARTLIB:MEMBER:...`), scanner mengenali keduanya |
| 19 | Self-Service | Member scan barcode buku → konfirmasi → loan dibuat (petugas = null); on/off di pengaturan |
| 20 | PWA | Manifest + ikon + maskable + shortcuts + standalone + install prompt |
| 21 | Offline Mode | Service Worker (cache shell/pages/assets, fallback offline.html) + **IndexedDB queue** transaksi + auto-sync saat online |
| 22 | Notifikasi | WhatsApp engine + Web Push (SW `push` handler) + email reset password |
| 23 | Denda Otomatis | Tarif per tipe member (Pelajar Rp500 / Staf Rp1.000 / Umum Rp2.000) + default di pengaturan |
| 24 | Payment Denda | Pelunasan cash / **QRIS** oleh petugas, status paid + WA konfirmasi lunas |
| 25 | Inventory | Sesi stock opname: scan → expected/scanned/missing progress real-time → laporan rusak/hilang/barcode tak dikenal |
| 26 | Laporan | 4 laporan (buku/peminjaman/member/denda) × periode harian-mingguan-bulanan-tahunan + **Export CSV** + Print/PDF |
| 27 | Audit Log | Semua aksi penting (login, CRUD, borrow, return, fine, setting) + old/new values + IP |
| 28 | Struktur DB | 19 tabel sesuai daftar (users, members, books, book_copies, loans, reservations, fines, whatsapp_messages, inventory_*, audit_logs, settings, dst.) |
| 29 | Relasi Utama | Member→Loans→BookCopy→Book, Reservations, Fines, Notifications ✓ |
| 30 | Architecture | Controllers tipis; logika di `app/Services` (Loan, Fine, Reservation, Notification, Barcode, Recommendation, Audit, Number) |
| 31 | Scheduled Automation | 07:00 reminder, 23:00 overdue+denda — via scheduler |
| 32 | API Ready | `/api/v1` + Sanctum token (login, me, books, my-loans, loans, returns, reservations, barcode/scan) — siap Flutter |
| 33 | UI/UX | Mobile-first 320px→1536px+, sidebar→drawer mobile, tabel→scroll/card, Tailwind + Alpine |
| 34 | Security | CSRF, XSS (Blade escaping), SQLi (Eloquent), rate limiting, CAPTCHA, bcrypt, RBAC, audit, validasi upload gambar |
| 35 | Fitur Modern | Semua fitur tabel section 35 terimplementasi (kecuali payment gateway eksternal: QRIS dicatat manual oleh petugas) |
| 36 | End-to-End | Member & Admin flow lengkap → Notification Engine → WhatsApp/Email/Push |

---

## 🌐 Fitur SaaS, Landing Page & Kelola Website

### Landing Page Publik (`/`)
- Hero + statistik, fitur, cara kerja, FAQ, CTA — **seluruh konten dari database & bisa diedit Super Admin** (menu 🌐 **Kelola Website**).
- Katalog pindah ke `/katalog`.
- **Paket langganan SaaS** (2 kartu harga):
  - 💳 **Bulanan** — Rp350.000/bulan *(harga perkenalan, coret Rp500.000)*
  - 🏆 **Beli Putus** — Rp7.500.000 sekali bayar *(coret Rp12.500.000, badge "PALING HEMAT")*
  - Catatan "Harga perkenalan untuk 5 sekolah pertama" — toggle-able.
- Tombol **"Langganan via WhatsApp"** → membuka `wa.me` nomor Super Admin dengan **pesan otomatis** sesuai paket (wording editable, placeholder `{app} {harga} {harga_resmi} {promo}`).
- Saat slot promo habis: matikan "Mode harga perkenalan" di admin → landing otomatis menampilkan harga resmi tanpa coretan.

### Branding (Super Admin → Kelola Website → Brand)
- **Upload logo** → otomatis dipakai di: landing, navbar, sidebar, login, **favicon**, dan **ikon PWA** (192/512/maskable di-generate otomatis dari logo).
- **Upload favicon** terpisah (opsional).
- Tombol **Reset ke bawaan**.
- Tagline, nomor WhatsApp Super Admin (tujuan order), catatan & slot promo, serta wording pesan WhatsApp paket Bulanan & Beli Putus.

### Paket & Harga (Kelola Website → Paket)
- Edit nama, deskripsi, harga promo, harga resmi, periode, badge, daftar fitur, dan paket highlight — langsung tampil di landing.

### Struktur
- `landing_contents` (section: hero/stat/feature/step/plan/faq/cta) + settings `site_logo`, `site_favicon`, `site_tagline`, `wa_superadmin`, `promo_*`, `wa_text_*`.
- `App\Support\Site` — helper logo/ikon/favicon/manifest & URL WhatsApp.
- `/manifest.webmanifest` — **dinamis**, ikon mengikuti logo upload-an.

---

## 📂 Struktur Penting

```
app/
├── Console/Commands/     # library:reminders, library:overdue
├── Http/Controllers/
│   ├── Admin/            # Dashboard, Books, Loans, Returns, Members, Fines,
│   │                     # Reservations, Inventory, Reports, Settings, Whatsapp, Audit
│   ├── Api/              # /api/v1 (Sanctum)
│   ├── Auth/             # Login+CAPTCHA, Register, ForgotPassword
│   └── Member/           # Dashboard, Loans+extend, Reservations, Fines,
│                         # Favorites, Card QR, SelfService
├── Jobs/SendWhatsappMessage.php
├── Middleware/CheckRole.php
├── Models/               # 19 model
└── Services/             # Loan, Fine, Reservation, Notification, Barcode,
                          # Recommendation, Audit, Number
public/
├── manifest.json  sw.js  offline.html  icons/
└── vendor/               # html5-qrcode, qrcodejs, jsbarcode (local, tanpa CDN)
resources/views/          # 43 Blade views (Indonesia, mobile-first)
```

## 🧪 Skenario Uji Cepat

1. **Login member** → dashboard; klik 📷 Self Service → scan/input `9789290116421-003` → konfirmasi → struk + WA.
2. **Login petugas** → Transaksi Pinjam → pilih member → scan barcode → approve.
3. **Pengembalian terlambat** → login admin → Kembali → scan `9781491914290-001` (pinjaman Andi, terlambat) → denda otomatis.
4. **Reservasi** → member buka buku yang stoknya 0 → Reservasi → petugas kembalikan bukunya → member dapat notifikasi "siap diambil".
5. **Reminder** → `php artisan library:reminders` → cek Admin → WhatsApp Engine → Riwayat Pesan.
6. **Denda harian** → `php artisan library:overdue`.
7. **PWA** → buka di Chrome/HP → pasang; matikan koneksi → halaman tetap terbuka dari cache.
8. **API**: `POST /api/v1/login` → pakai token → `GET /api/v1/my-loans`.
# smart-library
