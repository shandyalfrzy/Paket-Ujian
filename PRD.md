# PRD: PaketUjian

Product Requirements Document · Diperbarui 19 November 2026 · Status: perencanaan, inisiasi project

Dokumen ini adalah acuan utama pengembangan dan konteks wajib saat memberi instruksi (prompt) kepada AI Coding Tools. Setiap fitur yang diminta ke AI harus merujuk ke ID kebutuhan di dokumen ini (contoh: "implementasikan F1 sesuai PRD").

---

## 1. Latar Belakang dan Permasalahan

Guru membutuhkan lebih dari satu versi ujian untuk mencegah contek, remedial, dan ujian susulan. Membuat versi tambahan secara manual memakan waktu. Meminta chatbot membuat variasi soal memang cepat, tetapi hasilnya tidak terverifikasi:

- kunci jawaban bisa salah;
- opsi bisa ambigu atau "sama-sama benar";
- tingkat kesulitan antar versi bisa timpang, sehingga siswa yang kebetulan mendapat versi lebih sulit dirugikan.

> Asumsi: pernyataan masalah ini berasal dari tim dan belum divalidasi dengan guru.

## 2. Tujuan dan Deskripsi Aplikasi

**PaketUjian adalah web berbasis agen AI yang mengubah satu bank soal berformat PDF menjadi beberapa paket ujian yang berbeda bentuk, konteks, atau cara berpikirnya, tetapi tetap menguji materi dan kompetensi yang sama.**

Contoh: 50 soal sumber diolah menjadi 2-3 paket baru. Alur guru hanya empat langkah: **upload, pilih jumlah versi, review (setuju/tolak), download.**

Ini sistem agen, bukan chatbot. Pipeline berisi beberapa agen yang bekerja berurutan dan berulang (loop generate, verifikasi, perbaiki), bukan satu prompt tunggal.

### Tujuan MVP

Guru bisa menghasilkan 3 paket setara dari 50 soal sumber tanpa menulis ulang soal, dengan hasil yang bisa dipercaya dan diukur.

- Mengubah 1 PDF bank soal (maks. 50 soal) menjadi 2-3 paket pilihan ganda dalam satu pekerjaan, tanpa intervensi selain review.
- Menjaga materi, kompetensi, dan level kognitif soal varian tetap sama dengan soal sumber.
- Menekan soal bermasalah lewat verifikasi otomatis sebelum guru melihatnya.
- Menghasilkan data evaluasi yang bisa dilaporkan (persentase lolos verifikasi, persentase disetujui, kesetaraan antar paket).
- Berjalan dengan biaya Rp0 dan bisa didemokan tanpa bergantung penuh pada kuota API.

### Pembeda dari chatbot biasa

- Label materi, kompetensi, dan level kognitif per soal sumber, dikunci saat membuat varian.
- Agen verifikasi dari model berbeda yang menjawab soal tanpa melihat kunci.
- Loop perbaikan otomatis sebelum soal sampai ke guru.
- Perakitan paket, kunci jawaban, dan lembar jawab siap cetak.

### Non-tujuan (pagar cakupan)

- Soal esai, isian, dan uraian.
- PDF hasil scan atau OCR.
- Soal dengan rumus, gambar, atau diagram.
- Editor soal di dalam web (guru hanya setuju atau tolak).
- Ujian daring dan manajemen kelas. Penilaian dibatasi pada koreksi pilihan ganda terhadap paket yang dibuat PaketUjian sendiri (F11-F12, prioritas P1), bukan platform penilaian umum.

## 3. Target Pengguna

| Pengguna | Deskripsi | Kebutuhan utama |
| --- | --- | --- |
| Guru (utama) | Guru mata pelajaran SMA/sederajat yang punya bank soal PDF | Beberapa versi ujian setara, cepat, dan bisa dipercaya |
| Pengunjung expo (sekunder) | Pengunjung demo di expo akademik | Mencoba alur dengan PDF contoh dan melihat hasil nyata |

Cakupan mata pelajaran: PPKn (Pendidikan Pancasila di Kurikulum Merdeka) sebagai utama; Bahasa Indonesia (soal kebahasaan tanpa wacana) sebagai kedua. Format soal: pilihan ganda A-E.

**User stories**

1. Sebagai guru, saya ingin mengunggah PDF bank soal agar tidak perlu mengetik ulang.
2. Sebagai guru, saya ingin memilih jumlah versi (2 atau 3) sesuai kebutuhan kelas.
3. Sebagai guru, saya ingin melihat progres per soal agar tahu bagian mana yang bermasalah.
4. Sebagai guru, saya ingin menyetujui atau menolak tiap soal baru agar hanya soal yang saya percaya masuk paket.
5. Sebagai guru, saya ingin soal yang ditolak diganti satu kali agar paket tetap lengkap.
6. Sebagai guru, saya ingin mengunduh paket, kunci jawaban, dan lembar jawab agar siap dicetak.
7. Sebagai guru, saya ingin diberi tahu jelas jika PDF tidak sesuai format.
8. Sebagai pengunjung expo, saya ingin mencoba dengan PDF contoh tanpa menyiapkan file.
9. Sebagai pengunjung expo, saya ingin melihat hasil nyata yang sudah tersimpan agar demo tetap jalan walau kuota AI habis.
10. (P1) Sebagai guru, saya ingin memasukkan jawaban siswa untuk paket yang sudah dibuat dan langsung mendapat skor serta rincian benar/salah per nomor, agar tidak mengoreksi satu per satu secara manual.
11. (P1) Sebagai guru, saya ingin mengunggah foto lembar jawab dan melihat jawaban terbaca otomatis yang bisa saya perbaiki sebelum disimpan, agar input jawaban lebih cepat.
12. (P1) Sebagai guru, saya ingin melihat ringkasan kelas (rata-rata, nomor yang paling sering salah) agar tahu materi mana yang perlu diulang.

## 4. Identifikasi Kebutuhan dan Fitur Utama

| Prioritas | Fitur |
| --- | --- |
| P0 (wajib) | Upload dan validasi PDF · Parsing ke data terstruktur · Analisis soal sumber · Generate varian · Verifikasi dengan loop perbaikan · Antrean dan progres per soal · Review setuju/tolak · Rakit paket dan ekspor · Mode demo · Log panggilan LLM |
| P1 (menyusul) | **Koreksi ujian (manual F11, dari foto lembar jawab F12)** · Soal berbasis wacana · Laporan kesetaraan antar paket · Cadangan penyedia LLM otomatis · Riwayat pekerjaan · Materi ajar sebagai acuan |
| P2 (tidak dibangun sekarang) | Esai/isian, PDF scan, soal rumus/gambar, akun multi-guru, integrasi platform ujian |

## 5. User Flow

**Alur guru**

```
Beranda -> Upload PDF + pilih jumlah versi (2/3)
   -> [validasi PDF] gagal -> pesan jelas, kembali ke Beranda
   -> Halaman Progres (status per soal, auto-refresh)
   -> Halaman Review (soal baru berdampingan dengan soal sumber: setuju / tolak)
        tolak -> regenerasi 1x -> verifikasi -> muncul lagi di Review
   -> Halaman Unduh (paket, kunci jawaban, lembar jawab dalam PDF/Word)
```

**Alur koreksi (P1, setelah ujian dilaksanakan)**

```
Halaman Koreksi -> pilih paket (A/B/C) milik bank soal yang sudah dirakit
   -> pilih cara input jawaban siswa:
        a) ketik manual (F11), atau
        b) unggah foto lembar jawab (F12) -> jawaban terbaca tampil di tabel
           -> guru memeriksa dan memperbaiki yang salah baca / ditandai "perlu dicek"
   -> simpan -> skor dan rincian benar/salah per nomor
   -> Ringkasan kelas (rata-rata, nomor paling sering salah)
```

**Pipeline agen di belakang layar**

```
[1] PARSING     teks PDF -> JSON terstruktur (soal, opsi A-E, kunci), validasi skema
[2] ANALISIS    label materi, kompetensi, level kognitif per soal (di-cache)
[3] GENERATE    varian per soal, label dari [2] dikunci
[4] VERIFIKASI  model BERBEDA menjawab tanpa kunci, cek ambiguitas
      lolos -> lanjut ke review
      gagal -> [5] PERBAIKI -> kembali ke [4] (maks. 2 kali) -> lewat batas = "gagal"
[6] REVIEW      setelah SEMUA soal selesai: setuju / tolak
[7] RAKIT       acak opsi, sebar posisi kunci, ekspor paket + kunci + lembar jawab
```

Status per soal varian: `dibuat -> diverifikasi -> perlu_perbaikan -> lolos -> disetujui / ditolak`, atau `gagal`.

## 6. Kebutuhan Fungsional

| ID | Kebutuhan | Kriteria penerimaan |
| --- | --- | --- |
| F1 | Unggah dan validasi PDF | Hanya PDF dengan lapisan teks diterima. PDF scan/tanpa teks ditolak dengan pesan jelas. Maks. 50 soal per pekerjaan. |
| F2 | Parsing ke data terstruktur | Teks diekstrak lalu dirapikan LLM menjadi JSON (soal, opsi A-E, kunci) dan divalidasi skema. Target 90% soal terbaca benar pada 5 PDF uji. Format di luar skema menghentikan pekerjaan dengan pesan yang menunjuk bagian bermasalah. |
| F3 | Analisis soal sumber | Tiap soal diberi label materi, kompetensi, level kognitif. Hasil disimpan dan tidak dihitung ulang. |
| F4 | Generate varian per soal | Guru memilih 2 atau 3 versi. Varian memakai label yang sama dengan soal sumber tetapi berbeda bentuk/konteks/cara berpikir. Tidak boleh mengulang soal sumber atau varian lain. |
| F5 | Verifikasi dengan loop perbaikan | Model berbeda dari generator menjawab tanpa melihat kunci dan menandai ambiguitas. Lolos jika jawaban sama dengan kunci dan tidak ambigu. Gagal = diperbaiki dan diverifikasi ulang, maks. 2 kali, lalu ditandai gagal dan tidak masuk paket. |
| F6 | Antrean dan progres per soal | Pekerjaan berjalan di latar belakang. Layar menampilkan status tiap soal dan diperbarui otomatis tanpa muat ulang halaman. |
| F7 | Review setelah semua selesai | Guru melihat soal baru berdampingan dengan soal sumber, memilih setuju atau tolak. Soal ditolak diganti satu kali regenerasi, diverifikasi, lalu di-review ulang. |
| F8 | Rakit paket dan ekspor | Paket hanya dari soal yang disetujui. Opsi diacak, posisi kunci tersebar merata. Unduh paket, kunci, lembar jawab (PDF atau Word). |
| F9 | Mode demo | Ada PDF contoh dan tombol memutar ulang hasil nyata tersimpan, sehingga demo jalan saat kuota API habis. |
| F10 | Log panggilan LLM | Tiap panggilan mencatat penyedia, model, peran agen, prompt, respons, durasi, hasil verifikasi. Log menjadi sumber data evaluasi. |

### Kebutuhan fungsional tambahan (P1: dikerjakan hanya setelah P0 selesai)

| ID | Kebutuhan | Kriteria penerimaan |
| --- | --- | --- |
| F11 | Koreksi dengan input jawaban manual | Guru memilih paket yang sudah dirakit lalu memasukkan jawaban A-E per nomor untuk tiap siswa. Sistem membandingkan dengan **kunci akhir paket setelah pengacakan opsi (F8)**, lalu menampilkan skor dan benar/salah per nomor. Siswa diidentifikasi dengan kode atau nomor absen, bukan nama. Ringkasan kelas menampilkan rata-rata dan nomor yang paling sering salah. |
| F12 | Koreksi dari foto lembar jawab (eksperimental) | Guru mengunggah foto lembar jawab yang dibuat dari F8. Model vision membaca jawaban per nomor menjadi JSON dan divalidasi skema. Hasil **selalu** ditampilkan di tabel yang bisa diperbaiki guru sebelum disimpan. Nomor yang kosong, ganda, atau tidak terbaca ditandai "perlu dicek". **Syarat mulai dibangun:** spike dengan foto uji lolos ambang akurasi (lihat bagian 12). Jika spike gagal, F12 dibatalkan dan F11 menjadi satu-satunya jalur koreksi. |

Prasyarat di F8: lembar jawab dirancang agar mudah dibaca mesin (kode paket tercetak di kepala lembar, kotak jawaban besar dan berjarak, satu halaman, area identitas siswa terpisah). Sistem menyimpan urutan opsi dan kunci akhir tiap paket setelah pengacakan, karena koreksi bergantung padanya.

## 7. Kebutuhan Nonfungsional

| Aspek | Kebutuhan |
| --- | --- |
| Biaya | Rp0. Hanya jatah gratis API dan infrastruktur lokal. |
| Kuota | Cache analisis, antrean dengan jeda antar panggilan, retry dengan backoff untuk HTTP 429, batas percobaan perbaikan. |
| Kinerja | 50 soal x 3 versi selesai dalam 15 menit atau kurang (usulan, dikalibrasi setelah uji). |
| Privasi | Soal untuk demo dan evaluasi harus publik atau buatan sendiri. Tidak ada data siswa yang dikirim ke penyedia LLM. Pada fitur koreksi, lembar jawab memakai kode siswa; area identitas tidak dikirim ke model (dipotong atau tidak difoto) dan data siswa hanya disimpan di database lokal. Untuk demo dan evaluasi, pakai lembar jawab buatan sendiri. |
| Keandalan | PDF tidak sesuai format, penyedia LLM kena batas, atau respons bukan JSON valid tidak boleh membuat pekerjaan macet tanpa pesan. |
| Keterlacakan | Setiap varian dapat ditelusuri ke soal sumber, prompt, dan hasil verifikasinya. |
| Keterpakaian | Alur guru maksimal empat langkah. Tampilan responsif untuk laptop; layar demo expo terbaca dari jarak dekat. |
| Keterpeliharaan | Semua panggilan LLM lewat satu interface `LlmClient`, penyedia dipilih lewat `.env`. |

## 8. Gambaran Awal Antarmuka

Wireframe kasar, akan disempurnakan saat implementasi.

**Halaman Beranda / Upload** (halaman pertama yang diimplementasikan)

```
+--------------------------------------------------------------+
| PaketUjian                                      [Mode Demo]  |
+--------------------------------------------------------------+
|  Ubah satu bank soal menjadi beberapa paket ujian setara     |
|                                                              |
|  1. Unggah PDF bank soal (maks. 50 soal, PDF berteks)        |
|     +------------------------------------------+             |
|     |  Tarik file ke sini atau [Pilih file]    |             |
|     +------------------------------------------+             |
|     Belum punya file? [Pakai PDF contoh]                     |
|                                                              |
|  2. Mata pelajaran   [PPKn v]   Kurikulum [Merdeka v]        |
|  3. Jumlah versi     ( ) 2   (o) 3                           |
|                                                              |
|                                  [ Mulai Proses ]            |
+--------------------------------------------------------------+
```

**Halaman Progres**

```
Bank soal: PPKn-Kelas-X.pdf          Versi: 3        [Memproses 34/50]
+------+-------------------+------------+-----------+----------+
| No   | Soal sumber       | Paket A    | Paket B   | Paket C  |
+------+-------------------+------------+-----------+----------+
| 1    | Pancasila sebagai | lolos      | diperbaiki| lolos    |
| 2    | Hak dan kewajiban | diverifikasi| dibuat   | dibuat   |
| ...  |                   |            |           |          |
+------+-------------------+------------+-----------+----------+
Diperbarui otomatis tiap beberapa detik
```

**Halaman Review**

```
Soal 7 dari 48                               Paket B
+-----------------------------+-----------------------------+
| SOAL SUMBER                 | SOAL BARU                   |
| ...pertanyaan...            | ...pertanyaan...            |
| A. ...  B. ...  C. ...      | A. ...  B. ...  C. ...      |
| Kunci: C                    | Kunci: B  (lolos verifikasi)|
| Materi: ... Level: C3       | Materi: ... Level: C3       |
+-----------------------------+-----------------------------+
                            [ Tolak ]   [ Setuju ]
```

**Halaman Unduh**

```
Paket siap: A (47 soal)  B (46 soal)  C (45 soal)
[Unduh PDF]  [Unduh Word]   Kunci jawaban   Lembar jawab
Ringkasan: 3 soal gagal verifikasi dan tidak dimasukkan
```

**Halaman Koreksi (P1)**

```
Koreksi ujian    Bank soal: PPKn-Kelas-X     Paket: [B v]
+---------------------------------------------------------+
| Input jawaban:  (o) Ketik manual   ( ) Foto lembar jawab|
|                                                         |
| Siswa: [kode/no. absen: 07]         [Unggah foto]       |
| No | Jawaban terbaca | Kunci | Hasil                    |
| 1  | C               | C     | benar                    |
| 2  | B               | D     | salah                    |
| 3  | ?  (perlu dicek)| A     | -                        |
| ...                                                     |
|                              Skor: 38/46      [Simpan]  |
+---------------------------------------------------------+
Ringkasan kelas: rata-rata 31,2 | paling sering salah: no. 12, 27
```

## 9. Arsitektur dan Stack Teknologi

| Teknologi | Peran |
| --- | --- |
| **Laravel** | Backend: routing, validasi, model/Eloquent, queue, penyimpanan file, pemanggilan API LLM |
| **Vue.js** | Frontend: halaman interaktif (upload, progres, review) |
| **Inertia.js** | Penghubung Laravel dan Vue: halaman Vue dirender dari controller Laravel tanpa membangun API REST terpisah |

Pilihan pendukung:

| Kebutuhan | Pilihan |
| --- | --- |
| Starter kit | Laravel installer, starter kit Vue (Inertia) |
| Database | SQLite untuk development |
| Ekstrak teks PDF | `smalot/pdfparser` atau `spatie/pdf-to-text` (butuh poppler). Teks mentah dirapikan LLM ke JSON, bukan regex. |
| Agen | Class per agen (`AgentAnalisis`, `AgentGenerate`, `AgentVerifikasi`, `AgentPerbaiki`) memanggil `LlmClient` lewat HTTP client bawaan Laravel |
| Pekerjaan latar belakang | Laravel Queue, driver database (tanpa Redis) |
| Progres otomatis | Polling dari Vue lewat Inertia (partial reload) |
| Ekspor | DomPDF (PDF), PhpWord (Word) |
| Koreksi lembar jawab (P1) | Model vision lewat `LlmClient` (misalnya Gemini) dengan output JSON terstruktur. Perlu diuji dulu, lihat F12. |
| Deploy untuk expo | Jalan lokal (`php artisan serve` dan `queue:work`) |

> Catatan: dokumen ide awal memakai Livewire/Blade. Keputusan stack di PRD ini menggantinya dengan Vue + Inertia sesuai ketentuan tugas.

## 10. Model Data

| Tabel | Isi utama |
| --- | --- |
| `bank_soal` | Berkas unggahan, mata pelajaran, kurikulum, jumlah versi, status pekerjaan |
| `stimulus` | Teks wacana dan relasi ke bank soal (kosong di MVP, dipakai di P1) |
| `soal_sumber` | Soal, opsi A-E, kunci, label materi, kompetensi, level kognitif |
| `paket` | Versi A/B/C dan status perakitan |
| `soal_varian` | Isi soal baru, opsi, kunci, status, jumlah percobaan, alasan gagal, keputusan guru |
| `llm_log` | Penyedia, model, peran agen, prompt, respons, durasi, hasil |
| `hasil_koreksi` (P1) | Paket, kode siswa, skor, sumber input (manual/foto), waktu |
| `jawaban_siswa` (P1) | Hasil koreksi, nomor, jawaban terbaca, jawaban akhir setelah dicek guru, status (terbaca/perlu dicek), benar/salah |

Tabel `paket` atau `soal_varian` juga menyimpan urutan opsi dan kunci akhir setelah pengacakan (F8), karena dipakai untuk koreksi.

## 11. Pilihan LLM API

Angka kuota gratis sering berubah. Cek dashboard masing-masing sebelum memutuskan; jangan menganggap angka di bawah pasti.

| Penyedia | Catatan |
| --- | --- |
| Google Gemini (AI Studio) | Model Flash/Flash-Lite punya tier gratis. Limit berbeda antar sumber. Data tier gratis bisa dipakai penyedia. |
| Mistral (Experiment plan) | Gratis dengan verifikasi nomor HP. Kuota token besar, cocok untuk batch lewat antrean. |
| Groq | Gratis, model open-source, cepat, limit per menit/hari ketat. |
| OpenRouter | Model `:free` dengan limit harian rendah, kurang cukup untuk satu pekerjaan. |

Rekomendasi: uji Gemini dan Mistral di awal; sediakan urutan cadangan penyedia lewat `.env`; pakai model berbeda untuk generator dan verifikator; validasi hasil JSON; uji kualitas Bahasa Indonesia SMA pada sekitar 10 soal di 2-3 model.

## 12. Metrik Keberhasilan dan Evaluasi

Semua target adalah usulan awal dan dikalibrasi setelah uji.

| Metrik | Cara ukur | Target |
| --- | --- | --- |
| Akurasi parsing | Soal terbaca benar / total, pada 5 PDF uji | >= 90% |
| Lolos verifikasi percobaan pertama | Lolos tanpa perbaikan / total varian | >= 70% |
| Lolos setelah maks. 2 perbaikan | Lolos / total varian | >= 90% |
| Kesesuaian label | Varian dengan label sama dengan sumber, dinilai agen penilai berbeda lalu disampel manual | >= 90% |
| Kesetaraan antar paket | Selisih proporsi jawaban benar oleh agen penjawab antar paket | <= 10 poin persentase |
| Disetujui reviewer | Disetujui / ditinjau | Dilaporkan apa adanya |
| Waktu pekerjaan | 50 soal x 3 versi | <= 15 menit |
| Biaya | Total pengeluaran | Rp0 |
| Akurasi baca lembar jawab (P1, F12) | Nomor terbaca benar / total nomor, pada foto uji (berbagai cahaya dan sudut) | Ambang ditetapkan setelah spike; usulan awal >= 95% dan belum divalidasi |
| Kecocokan skor koreksi (P1) | Skor sistem dibanding hitungan manual pada sampel lembar jawab | 100% pada input yang sudah dicek guru |

**Evaluasi tanpa akses guru:** penilaian utama memakai model AI berbeda dengan rubrik tertulis. Keterbatasannya harus ditulis jujur di laporan: penilaian AI atas keluaran AI bisa sirkular dan cenderung terlalu permisif. Penguat: sampel manual tim (sekitar 30 soal acak), penilai manusia bila ada (dosen, kakak tingkat, guru kenalan), dan angka dari `llm_log`.

## 13. Risiko dan Mitigasi

| Risiko | Mitigasi |
| --- | --- |
| Parsing PDF di PHP rapuh | Kunci format input, uji 3-5 PDF berbeda sejak awal, rapikan lewat LLM ke JSON, layanan Python hanya cadangan |
| Kuota gratis habis atau aturan berubah | Abstraksi `LlmClient`, cadangan penyedia, cache analisis, mode demo |
| Soal PPKn ambigu | Prompt ketat soal pengecoh, verifikator menandai ambiguitas, batas perbaikan 2 kali |
| Kesalahan fakta (pasal, bunyi sila, tahun) | Verifikasi model berbeda, materi ajar sebagai acuan (P1), sampel manual |
| Evaluasi AI-saja kurang meyakinkan | Sampel manual dan penilai manusia, tulis keterbatasan |
| Data bocor lewat tier gratis | Hanya soal publik atau buatan sendiri |
| Scope creep, tim 5 orang tidak sinkron | Non-tujuan sebagai pagar, P1 hanya setelah P0 selesai, rapat singkat mingguan |
| Sisa waktu lebih pendek dari rencana awal | Jadwal disesuaikan di bagian 14, P0 yang bisa disederhanakan dipotong |
| Foto lembar jawab salah terbaca (cahaya, sudut, tulisan tangan, bulatan terisi) | Lembar jawab dirancang ramah mesin, guru selalu memeriksa hasil sebelum disimpan, tanda "perlu dicek", spike sebelum dibangun, F11 sebagai jalur cadangan |
| Lembar jawab berisi data siswa dikirim ke penyedia LLM | Identitas tidak difoto atau dipotong sebelum dikirim, pakai kode siswa, demo hanya dengan lembar jawab buatan sendiri |
| Koreksi menambah beban di tengah tenggat | Statusnya P1: baru dikerjakan setelah alur P0 jalan; F11 dulu, F12 hanya jika spike lolos |

## 14. Rencana Kerja

Dokumen ide awal merencanakan 12 minggu dari 10 Oktober 2026. Per 19 November 2026 sisa waktu sekitar 6-7 minggu menuju tenggat sekitar awal Januari 2027. **Tanggal tenggat dan expo pasti perlu dikonfirmasi.** Usulan jadwal dipadatkan:

| Minggu | Fokus | Hasil |
| --- | --- | --- |
| 1 (sekarang) | Inisiasi: project, repo, PRD, halaman upload; spike parsing PDF dan satu panggilan LLM sampai JSON valid | Aplikasi jalan lokal, keputusan penyedia LLM |
| 2-3 | Pipeline agen (analisis, generate, verifikasi, perbaikan) dan antrean | Pekerjaan end-to-end |
| 4 | UI progres dan review | Alur guru di web |
| 5 | Ekspor, mode demo, evaluasi dengan sampel | Data metrik |
| 6-7 | Laporan, penyempurnaan, persiapan expo | Dokumentasi final dan demo siap |

Pembagian peran usulan (5 orang): (1) ekstraksi PDF dan format input; (2) agen generate dan prompt; (3) agen verifikasi, loop perbaikan, antrean; (4) UI upload, progres, review; (5) ekspor, evaluasi, dokumentasi.

## 15. Rencana Demo Expo

- Contoh PDF PPKn siap pakai di aplikasi.
- Satu tombol memutar ulang hasil nyata tersimpan (mode demo).
- Halaman metrik yang menampilkan angka evaluasi.
- Keluaran siap cetak (paket, kunci, lembar jawab) dipajang sebagai contoh fisik.
- Laptop cadangan dan hasil run sukses tersimpan.

## 16. Pertanyaan Terbuka

| # | Pertanyaan | Blokir? |
| --- | --- | --- |
| 1 | Tanggal tenggat dan expo yang pasti? | Ya |
| 2 | Kurikulum yang dipakai (K13 atau Merdeka) dan nama mapel di UI? | Tidak |
| 3 | Penyedia LLM utama setelah uji perbandingan (Gemini atau Mistral)? | Ya, sebelum pipeline |
| 4 | Sumber PDF bank soal PPKn contoh (publik atau buatan sendiri)? | Ya, untuk uji parsing |
| 5 | Format ekspor prioritas: PDF, Word, atau keduanya? | Tidak |
| 6 | Penilai manusia untuk sampel evaluasi? | Tidak |
| 7 | Apakah dosen mensyaratkan jumlah agen atau format laporan tertentu? | Tidak |
| 8 | Fitur login bawaan starter kit dipertahankan atau dihapus untuk MVP? | Tidak |
| 9 | Apakah model vision penyedia gratis yang dipilih cukup akurat membaca foto lembar jawab? (uji dengan 10-20 foto) | Ya, sebelum F12 dibangun |
| 10 | Bentuk lembar jawab yang dipakai: kotak huruf tulisan tangan atau bulatan yang diarsir? | Ya, untuk desain F8 dan spike F12 |