# PaketUjian

Web berbasis agen AI yang mengubah satu bank soal PDF menjadi beberapa paket ujian pilihan ganda yang setara (materi, kompetensi, dan level kognitif sama), lengkap dengan kunci jawaban dan lembar jawab siap cetak.

Alur guru: **upload PDF → pilih jumlah versi → review (setuju/tolak) → download.**

Proyek akhir mata kuliah Pemrograman Web 2. Kebutuhan lengkap ada di [PRD.md](PRD.md).

## Teknologi

| Teknologi | Peran |
| --- | --- |
| Laravel | Backend: routing, database, antrean, pemanggilan LLM |
| Vue.js | Frontend: halaman upload, progres, review |
| Inertia.js | Penghubung Laravel dan Vue tanpa API REST terpisah |
| SQLite | Database development |

## Prasyarat

- PHP 8.2 atau lebih baru (cek versi minimum di `composer.json`)
- Composer
- Node.js 20 atau lebih baru dan npm
- Git

## Cara Menjalankan (Lokal)

```bash
git clone https://github.com/shandyalfrzy/Paket-Ujian.git
cd Paket-Ujian

composer install
cp .env.example .env
php artisan key:generate

# SQLite: pastikan file database ada, lalu migrasi
touch database/database.sqlite
php artisan migrate

npm install
```

Jalankan aplikasi (server, antrean, dan Vite sekaligus):

```bash
composer run dev
```

Lalu buka `http://localhost:8000`.

Jika skrip `composer run dev` tidak tersedia, jalankan di tiga terminal terpisah:

```bash
php artisan serve
php artisan queue:work
npm run dev
```

## Konfigurasi LLM

Penyedia LLM dipilih lewat `.env` (akan ditambahkan saat pipeline agen dibangun). Jangan pernah meng-commit file `.env` atau API key.

## Struktur Dokumen

| Berkas | Isi |
| --- | --- |
| [PRD.md](PRD.md) | Kebutuhan produk, fitur, alur, dan rencana kerja |
| [docs/](docs/) | Dokumentasi hasil pengerjaan setiap pertemuan |

## Status

Tahap: inisiasi project. Lihat [docs/pertemuan-01.md](docs/pertemuan-01.md).

## Anggota Kelompok

| Nama | NIM | Peran |
| --- | --- | --- |
| (isi) | (isi) | (isi) |
