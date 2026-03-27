# 🏡 Rasmita's Family Gathering 2026
### Aplikasi Game Interaktif Reuni Keluarga — Gathering & Games
> **Single-file web app** — tidak perlu server backend, tidak perlu install. Buka browser, data tersimpan di Supabase cloud!

---

## 📁 File-file Utama

| File | Fungsi | Dibuka oleh |
|------|--------|-------------|
| `rasmita-family-gathering-2026.html` | **Game utama** — landing page, lobby, 6 game interaktif | Semua peserta di HP |
| `admin.html` | **Admin Panel** — upload foto, setting event, kontrol game, sing-along | Host/Admin saja |
| `proyektor.html` | **Tampilan proyektor** — live semua game di layar besar | Laptop terhubung proyektor |
| `SUPABASE-SETUP.sql` | Script SQL untuk inisialisasi database Supabase | Developer / admin teknis |
| `netlify.toml` | Konfigurasi deploy ke Netlify | Otomatis (tidak perlu diedit) |

---

## ⚙️ Setup Supabase (Sekali Sebelum Event)

### Langkah 1 — Buat Project Supabase

1. Buka **https://supabase.com** → Sign up / Login
2. Klik **"New Project"**, isi:
   - **Name:** `rasmita-2026`
   - **Database Password:** buat password kuat (simpan!)
   - **Region:** `Southeast Asia (Singapore)` ← paling dekat Bandung
3. Klik **"Create new project"** → tunggu ~2 menit
4. Catat **Project URL** dan **anon/public key** di Settings → API

### Langkah 2 — Jalankan SQL Setup

1. Buka **Supabase Dashboard → SQL Editor**
2. Copy isi file `SUPABASE-SETUP.sql` → paste → klik **"Run"**
3. Pastikan tidak ada error merah

Script akan otomatis membuat **13 tabel** + RLS policies + mengaktifkan Realtime.

### Langkah 3 — Buat Storage Buckets

Di **Storage** sidebar, buat 2 bucket:

| Bucket Name | Public | Keterangan |
|-------------|--------|------------|
| `rasmita-photos` | ✅ Public | Foto keluarga, foto profil, foto komedian |
| `rasmita-audio` | ✅ Public | File MP3 lagu sing-along |

Untuk setiap bucket, tambahkan **Policies**: Allow SELECT, INSERT, UPDATE for everyone (anon).

### Langkah 4 — Aktifkan Realtime

Di **Database → Replication**, aktifkan tabel-tabel berikut:
`ras_players`, `ras_open_games`, `ras_g3_live`, `ras_g5_results`, `ras_g6_singalong`, `ras_g6_play_cmd`, `ras_g1_scores`, `ras_g4_scores`

### Langkah 5 — Masukkan Credentials ke File HTML

Buka **ketiga file HTML** dan ganti nilai berikut di bagian `<script>`:

```javascript
const SUPABASE_URL = 'https://XXXX.supabase.co';      // ← ganti XXXX
const SUPABASE_ANON_KEY = 'eyJhbGciOiJIUzI1NiIs...';  // ← ganti dengan anon key
```

> **Catatan:** Semua file (`rasmita-family-gathering-2026.html`, `admin.html`, `proyektor.html`) sudah berisi credentials aktif (`ykibpxiovrslknagmnpq.supabase.co`) dengan **Anon Key JWT** yang valid. Tidak perlu edit manual.

---

## 🗃️ Struktur Database Supabase

| Tabel | Isi | Realtime |
|-------|-----|----------|
| `ras_event` | Setting event: nama, tanggal, venue, room_code | — |
| `ras_members` | Data anggota keluarga + foto_url | — |
| `ras_players` | Peserta yang sudah join + photo_url | ✅ |
| `ras_open_games` | Game yang dibuka admin (array game_ids) | ✅ |
| `ras_g3_questions` | Pertanyaan custom Game 3 | — |
| `ras_g3_live` | Data voting live → proyektor | ✅ |
| `ras_g5_results` | Hasil Mirip Siapa per peserta → proyektor + lobby | ✅ |
| `ras_g6_singalong` | Status sing-along → proyektor + HP peserta | ✅ |
| `ras_g6_play_cmd` | Perintah play dari admin → HP peserta | ✅ |
| `ras_g1_scores` | Skor Memory Match → proyektor | ✅ |
| `ras_g4_scores` | Skor Timeline → proyektor | ✅ |
| `ras_lagu_storage` | URL MP3 + lirik per lagu | — |
| `ras_komedian_photos` | Foto komedian (URL dari Storage) | — |

### Batas Gratis Supabase Free Plan

| Resource | Batas |
|----------|-------|
| Database | 500 MB |
| Storage | 1 GB |
| Bandwidth | 2 GB/bulan |
| API requests | 50.000/hari |
| Realtime connections | 200 concurrent |

> Cukup untuk family gathering 50–100 orang selama 1 hari penuh.

---

## 🚀 Cara Mulai

### Mode Online (Rekomendasi)

```
1. Setup Supabase (sekali saja, langkah di atas)
2. Upload seluruh folder ke Netlify / Vercel (drag & drop, gratis)
3. Dapatkan URL publik → share ke semua peserta via WhatsApp
4. Admin buka admin.html di URL yang sama
5. Proyektor buka proyektor.html di laptop yang terhubung layar besar
```

### Mode Offline (Tanpa Internet)

```
1. Buka admin.html di browser laptop
2. Upload foto anggota keluarga (min 10 foto)
3. Set kode room di Pengaturan Event
4. Hubungkan laptop ke proyektor via HDMI
5. Buka proyektor.html → F11 fullscreen → tampil di layar besar
6. Peserta buka rasmita-family-gathering-2026.html di HP
7. Buka game satu per satu dari Admin Panel
```

> ⚠️ Mode offline: Supabase tetap butuh internet untuk sync data. Jika internet tidak ada, real-time tidak berjalan.

---

## 📽️ Setup Proyektor / TV

```
[Laptop Admin]   ──HDMI──> [Proyektor/TV]  ← proyektor.html fullscreen
[HP Peserta]     ──WiFi──> [URL Game]       ← tiap orang main sendiri
[Browser Admin]  ──────>   admin.html       ← kontrol semua game
```

**Proyektor otomatis menampilkan (refresh tiap 2 detik) melalui 7 layar:**

| Screen | Konten |
|--------|--------|
| `screenWelcome` | Welcome screen + cara bergabung + daftar 6 game |
| `screenLobby` | Semua peserta live yang sudah join |
| `screenGame1` | Leaderboard Memory Match |
| `screenGame3` | Hasil voting Siapa Paling real-time |
| `screenGame4` | Leaderboard Timeline |
| `screenGame5` | Hasil Mirip Siapa per peserta |
| `screenGame6` | Lirik sing-along BESAR saat Tebak Lagu |

---

## 🎮 6 Game yang Tersedia

| # | Game | Pemain | Durasi | Perlu Foto |
|---|------|--------|--------|------------|
| 🃏 1 | Memory Match Foto | **2–4 dipilih admin** | 15 mnt | ✅ Ya |
| 🔍 2 | Tebak Siapa? | Semua | 20 mnt | ✅ Ya |
| 🗳️ 3 | Siapa yang Paling...? | Semua | 15 mnt | ✅ Ya |
| ⏳ 4 | Timeline Keluarga | Semua | 15 mnt | ✅ Ya |
| 🎭 5 | Kamu Mirip Siapa? | Individual | 5 mnt | ❌ Tidak |
| 🎵 6 | Tebak Lagu Indonesia | Semua | 10 mnt | ❌ Tidak |

---

## 🃏 GAME 1 — Memory Match Foto

**Pemain:** 2–4 orang per meja, 1 HP dibagi | **Durasi:** 10–15 menit | **Foto minimal:** 8

### Cara Main
1. Satu pemain pegang HP. Tap kartu pertama → foto terbalik
2. Tap kartu kedua → jika **cocok**: +10 poin, giliran lanjut
3. Jika **tidak cocok**: kedua kartu tutup, giliran berpindah
4. Lanjut sampai semua pasangan ditemukan

### Sistem Poin

| Kondisi | Poin |
|---------|------|
| Pasangan cocok | +10 |
| Tidak cocok | 0 |

### Pemilihan Pemain
Saat masuk Game 1, muncul layar **Pilih Pemain**:
- Peserta yang sudah join otomatis muncul sebagai pilihan
- Pilih 1–4 pemain yang akan bermain
- Bisa tambah nama manual dengan tombol **+ Tambah**
- Klik **▶ Mulai Game!**

### Aturan
- ❌ Tidak boleh menunjuk/memberi tahu posisi kartu ke pemain lain
- ✅ Boleh berteriak kegirangan!

### Script MC
> *"Ingat-ingat posisi kartu yang sudah dibuka! Itu kunci menang. Yang ingatannya bagus, waktunya unjuk gigi! 4 orang bergantian, 1 HP dibagi bersama."*

---

## 🔍 GAME 2 — Tebak Siapa?

**Pemain:** Semua, 1 HP per orang | **Durasi:** 15–20 menit | **Foto minimal:** 10

### Sistem Poin (Menurun Seiring Waktu)

| Waktu | Poin |
|-------|------|
| Sangat awal (foto masih blur) | 400–500 |
| Cukup awal | 300–400 |
| Setelah sebagian terlihat | 200–300 |
| Foto sudah jelas | 100–200 |
| Salah | 0 |

### Aturan
- ❌ Dilarang tanya ke orang lain
- ✅ Nama panggilan diterima (Om Budi = Budi = ok)
- ✅ Ejaan kurang tepat masih ok

### Script MC
> *"Foto pertama selalu yang paling susah karena masih blur total. Tapi kalau kamu kenal betul, pasti bisa tebak dari rambut atau bentuk muka aja! Jangan tunggu jelas — rugi poin!"*

---

## 🗳️ GAME 3 — Siapa yang Paling...?

**Pemain:** Semua serentak, 1 HP per orang | **Durasi:** 10–15 menit | **Timer per pertanyaan:** 30 detik

### Sistem Poin

| Kondisi | Poin |
|---------|------|
| Dipilih oleh pemain lain | +5 per suara |
| Pilih orang yang paling banyak dipilih | +10 |

### 10 Pertanyaan Default
1. Siapa yang paling suka tidur?
2. Siapa yang paling cerewet?
3. Siapa yang paling sering telat?
4. Siapa yang paling lebay kalau sakit?
5. Siapa yang paling banyak makan?
6. Siapa yang paling susah bangun pagi?
7. Siapa yang paling gampang nangis?
8. Siapa yang paling keras kepala?
9. Siapa yang paling suka selfie?
10. Siapa yang paling bawel kalau lapar?

### Edit Pertanyaan
**Admin Panel → 🗳️ Game 3 → Tab ❓ Edit Pertanyaan:**
- Edit teks pertanyaan langsung di admin
- Tambah / hapus pertanyaan
- Reset ke pertanyaan default
- Klik **💾 Simpan** — langsung aktif di game peserta

### Aturan
- ✅ Boleh pilih diri sendiri!
- ✅ Voting anonim, tidak ada yang tahu
- ❌ Tidak boleh protes kalau banyak yang pilih kamu 😂

### Tips Proyektor ⭐
Setelah semua vote masuk, **tampilkan hasil di proyektor** — ini momen paling heboh!

### Script MC
> *"Jujur ya! Kalau ada yang tersinggung... itu berarti semua sepakat! Ini bukan celaan, ini sayang! 😂 Yang paling sering dapat suara, nanti ada 'penghargaan' khusus!"*

---

## ⏳ GAME 4 — Timeline Keluarga

**Pemain:** Semua, 1 HP per orang | **Durasi:** 10–15 menit | **Ronde:** 3 ronde × 5 foto

### Cara Main
Drag & drop 5 foto anggota keluarga dari **kiri (termuda)** ke **kanan (tertua)**. Kunci jawaban saat sudah yakin.

### Sistem Poin

| Kondisi | Poin |
|---------|------|
| Urutan benar semua (5/5) | +100 |
| 4 dari 5 benar | +70 |
| 3 dari 5 benar | +40 |
| Kurang dari 3 benar | 0 |
| Bonus kecepatan < 10 detik | +50 |
| Bonus kecepatan 10–20 detik | +25 |

### Aturan
- ❌ Tidak boleh tanya langsung ke orangnya
- ✅ Boleh estimasi dari foto (rambut putih, kerutan, dll)

### Script MC
> *"Kamu tau nggak usia pasti semua anggota keluarga ini? Sekarang saatnya membuktikan! Kalau salah, berarti kamu kurang perhatian! 😄"*

---

## 🎭 GAME 5 — Kamu Mirip Siapa?

**Pemain:** Individual, 1 HP per orang | **Durasi:** ~5 menit per orang | **Database:** 200 komedian Indonesia

### 6 Pertanyaan
1. Jenis kelamin
2. Kisaran usia
3. Postur tubuh
4. Jenis rambut
5. Ciri khas wajah
6. Karakter/gaya humor

### Tidak Ada Poin Kompetitif — Murni Hiburan! 🎉
Screenshot hasil dan share ke grup! Tampilkan semua hasil di proyektor.

### Ritual Seru
Setelah semua dapat hasil, yang dapat komedian sama **kumpul satu grup → foto bareng**!

### Script MC
> *"Yang dapat Nunung, Sule, atau Raditya Dika — selamat ditakdirkan jadi bintang! Yang dapat komedian yang nggak kalian kenal... berarti kalian unik banget sampai algoritma bingung! 😂"*

### Database Komedian — Top Referensi

**👨 Komedian Pria (Top 10):**

| Nama | Tagline |
|------|---------|
| Sule | *"Emang gue pikirin!"* |
| Raditya Dika | *"Hidup itu absurd"* |
| Ernest Prakasa | *"Kamu udah dewasa, sekarang saatnya bayar pajak"* |
| Bintang Emon | *"Guys, serius..."* |
| Pandji Pragiwaksono | *"Indonesia masih bisa!"* |
| Ge Pamungkas | *"Kalau mau jujur ya..."* |
| Mongol Stres | *"Gue stres!"* |
| Soleh Solihun | *"Secara akademis..."* |
| Arie Kriting | *"Timur banget!"* |
| Babe Cabita | *"Saya mah orangnya..."* |

**👩 Komedian Wanita (Top 10):**

| Nama | Tagline |
|------|---------|
| Nunung | *"Aduh mak!"* |
| Kiky Saputri | *"Berani bicara!"* |
| Soimah | *"Duh gusti!"* |
| Rina Nose | *"Histeris tapi lucu!"* |
| Arafah Rianti | *"Relate sama kehidupan"* |
| Asri Welas | *"Nggak nyana!"* |
| Tika Panggabean | *"Totalitas!"* |
| Mpok Nori | *"Ooooh biyung!"* |
| Ayu Ting Ting | *"Bohoso opo iku?"* |
| Ria Ricis | *"Ricis banget!"* |

### Menambahkan Foto Komedian ke Game 5

Game 5 saat ini hanya menampilkan **teks nama dan komentar** — belum ada foto. Untuk menambahkan:

**1. Dapatkan URL foto publik** (pilih salah satu):

```
Opsi A — Wikipedia (paling aman):
  Buka id.wikipedia.org/wiki/[Nama] → klik foto → "More details" → copy URL gambar

Opsi B — Imgur:
  Upload foto ke imgur.com → salin direct link (.jpg/.png)

Opsi C — Google Drive:
  Upload → Share "Anyone with the link"
  Ubah URL dari: drive.google.com/file/d/FILE_ID/view
  Menjadi: drive.google.com/uc?id=FILE_ID
```

**2. Update kode di HTML:**
```javascript
// Cari DATABASE_KOMEDIAN, lalu tambahkan field foto dan tagline:
{ id:1, nama:"Sule", usia:44, postur:"sedang", rambut:"lurus",
  karakter:"lebay",
  foto: "https://upload.wikimedia.org/wikipedia/commons/...",
  tagline: "Emang gue pikirin!",
  komentar:"Energi kayak Sule!" }
```

**3. Upload massal via Admin Panel:**
Admin Panel → 🎭 Komedian → Upload Foto Massal

---

## 🎵 GAME 6 — Tebak Lagu Indonesia

**Pemain:** Semua, 1 HP per orang | **Durasi:** 10 menit | **Lagu:** 5 lagu lintas generasi

### 5 Lagu

| # | Judul | Artis | Tahun |
|---|-------|-------|-------|
| 1 | 🌴 Tabola Bale | Silet Open Up feat. Jacson Zeran | 2025 |
| 2 | 🏡 Harta yang Paling Berharga | Hedi Yunus (OST Keluarga Cemara) | 1996 |
| 3 | 💃 Dansa Yok Dansa | Harvey Malaiholo | 1985 |
| 4 | ☕ Kopi Dangdut | Fahmi Shahab | 1987 |
| 5 | 🕺 Goyang Dumang | Cita Citata | 2014 |

### Sistem Poin

| Kondisi | Poin |
|---------|------|
| Benar tanpa petunjuk | 100 |
| Benar dengan petunjuk | 50 |
| Salah | 0 |
| **Maksimum** | **500** |

### Upload MP3 Chorus

1. Admin Panel → **🎵 Lagu (Game 6)**
2. Upload MP3 bagian chorus per lagu (maks 8MB)
3. Edit lirik sing-along langsung di kolom teks
4. Klik **💾 Simpan Lirik**

> Tanpa MP3: otomatis menggunakan melodi sintetis (Web Audio API)

### Alur Sing-Along Saat Event

```
1. Peserta mendengar melodi & menebak lagu di HP
2. Setelah semua selesai menjawab...
3. Admin Panel → Game 6 → Tab 🎤 Kontrol Sing-Along
4. Klik ▶ Play di lagu yang sedang berjalan
5. Musik + lirik muncul di semua HP peserta sekaligus
6. Lirik tampil BESAR di proyektor untuk nyanyi bareng
7. Klik ⏭ Next untuk lanjut ke lagu berikutnya
```

### Aturan
- ❌ Tidak boleh Googling!
- ✅ Boleh minta tolong dengarkan bersama
- ✅ Audio adalah nada sintetis — bukan rekaman asli; tekan petunjuk jika susah

### Script MC
> *"Audio di game ini adalah nada sintetis — bukan rekaman asli. Kalau susah ditebak, tekan petunjuk! Yang langsung tahu tanpa petunjuk — kamu legend musik Indonesia! Setelah terjawab, kita nyanyi bareng ya!"*

---

## 🏆 Sistem Hadiah (Rekomendasi)

| Kategori | Hadiah |
|----------|--------|
| Juara Overall (total semua game) | Hadiah utama 🥇 |
| Hall of Fame Game 3 (paling sering dipilih) | Piala lucu / doorprize 😄 |
| Game 5: dapat komedian teraneh | Hadiah kejutan |
| Game 6: nyanyi paling keras | Hadiah partisipasi 🎤 |
| Foto paling cocok | Hadiah kreatif |

---

## 📸 Upload Foto Keluarga

**Admin Panel → 📸 Foto Keluarga:**

| Tab | Keterangan |
|-----|------------|
| Semua Foto | Upload & kelola semua foto di sini |
| Memory Match | Preview foto untuk Game 1 |
| Tebak Siapa | Preview foto untuk Game 2 |
| Siapa Paling | Preview foto kandidat voting Game 3 |
| Timeline | Preview foto untuk Game 4 |

Upload minimal **10 foto**, ideal **15–20 foto**. Semua game otomatis menggunakan foto yang sama.

### Panduan Foto Keluarga

- **Format:** JPG atau PNG
- **Ukuran:** 300×300 px (square/persegi)
- **Kualitas:** Minimal 72 DPI untuk layar
- **Ekspresi:** Foto wajah jelas, terang
- **Background:** Bebas, tapi yang polos lebih mudah dikenali di game

### Cara Kumpulkan Foto:
```
1. Buat grup WhatsApp khusus "Foto Gathering 2026"
2. Minta setiap anggota kirim foto selfie/portrait terbaru
3. Admin kompres dan rename dengan format: 01-nama.jpg
4. Masukkan ke Admin Panel
```

---

## 🚀 Deploy & Publish

### Metode 1 — Netlify (Paling Mudah, Gratis) ✅ Direkomendasikan

```
1. Buka netlify.com → Sign up
2. Di dashboard → kotak "Want to deploy a new site without connecting to Git?"
3. Drag & drop seluruh folder project ke kotak tersebut
4. Tunggu 10-30 detik → URL otomatis terbuat
5. (Opsional) Site settings → "Change site name" → rasmita-gathering-2026
   URL: https://rasmita-gathering-2026.netlify.app
```

### Metode 2 — GitHub Pages

```
1. github.com → New repository → rasmita-gathering-2026 (Public)
2. Upload ketiga file HTML
3. Settings → Pages → Source: main / (root) → Save
4. URL: https://USERNAME.github.io/rasmita-gathering-2026
```

### Metode 3 — Vercel

```
1. vercel.com → Sign up → "Add New Project" → Upload
2. Upload folder project → Deploy
3. URL: https://rasmita-gathering-2026.vercel.app
```

### Membuat QR Code

Setelah dapat URL, buat QR Code untuk dipasang di lokasi acara:

```
1. Buka qrcode-monkey.com
2. Paste URL aplikasi
3. Warna: Foreground #2D5016 (hijau), Background #FDF8EE (krem)
4. Download PNG resolusi tinggi → print ukuran A4 atau A5
```

**Spesifikasi Print:** Ukuran minimal 5×5 cm, disarankan 10×10 cm. Format PNG/SVG (bukan JPG).

### Short URL

Gunakan [bitly.com](https://bitly.com) → buat custom link misalnya `bit.ly/rasmitas`

---

## ✅ Checklist Persiapan

### H-7 (Seminggu Sebelum)
- [ ] Download semua file dari paket ini
- [ ] Test buka `rasmita-family-gathering-2026.html` dan `admin.html` di Chrome/Edge
- [ ] Setup Supabase (jalankan SQL, buat bucket, aktifkan Realtime)
- [ ] Kumpulkan foto anggota keluarga via grup WhatsApp (min 10, ideal 20)
- [ ] Putuskan: pakai MP3 atau melodi sintetis untuk Game 6

### H-3 (3 Hari Sebelum)
- [ ] Upload semua foto keluarga di Admin Panel → Foto Keluarga
- [ ] Set nama event, tanggal, lokasi, dan kode room di Admin → Pengaturan Event
- [ ] Test semua 6 game berjalan normal
- [ ] Upload ke Netlify/Vercel, test URL
- [ ] Siapkan hadiah untuk pemenang
- [ ] Print halaman ATURAN GAME ini untuk MC

### H-1 (Sehari Sebelum)
- [ ] Cek baterai laptop / charger dibawa
- [ ] Cek kabel HDMI tersedia / adapter DisplayPort ke HDMI
- [ ] Test koneksi proyektor / TV di venue
- [ ] Backup seluruh folder game ke flashdisk (jaga-jaga)
- [ ] Siapkan speaker eksternal untuk audio Game 6
- [ ] Kirim link/QR code ke grup WhatsApp keluarga

### Hari H — 30 Menit Sebelum Acara
- [ ] Nyalakan proyektor/TV, hubungkan laptop
- [ ] Buka `rasmita-family-gathering-2026.html` di laptop → F11 fullscreen
- [ ] Tampilkan halaman landing (ada nama event, kode room, game list)
- [ ] Buka `admin.html` di tab berbeda (atau HP/laptop lain)
- [ ] Test audio: buka Game 6, tekan play, pastikan bunyi dari speaker
- [ ] Siapkan MC dengan script dari dokumen ini
- [ ] **JANGAN buka semua game dulu** — buka satu per satu saat giliran

---

## 📅 Urutan Acara (Rekomendasi)

```
[Jam 1] Registrasi peserta masuk room + foto profil
[Jam 2] Game 1: Memory Match Foto (hangat-hangat dulu)
[Jam 3] Game 2: Tebak Siapa? (semua main bareng)
[Jam 4] Game 3: Siapa yang Paling...? (voting seru, tampilkan di proyektor!)
[Jeda]  Makan / break
[Jam 5] Game 4: Timeline Keluarga
[Jam 6] Game 5: Kamu Mirip Siapa? (individual, share hasil ke grup)
[Jam 7] Game 6: Tebak Lagu + Sing-Along (penutup paling meriah!)
[Jam 8] Pengumuman pemenang overall
```

---

## 🎤 Script MC — Alur Lengkap

### Pembuka
```
"Halo semua! Selamat datang di Rasmita Family Gathering 2026 — Gathering & Games!
Kita punya 6 game seru yang bisa dimainkan dari HP kalian masing-masing.

Cara bergabung:
1. Buka browser di HP kalian
2. Ketik: [URL GAME atau scan QR code]
3. Masukkan nama kalian
4. Foto profil boleh diupload, boleh tidak
5. Klik 'Masuk ke Lobby'

Nanti kalau game sudah saya buka, nama gamenya akan aktif di lobby kalian.
[Tunggu semua peserta masuk]
Oke, semua sudah masuk? Kita mulai dari Game 1..."
```

### Transisi Antar Game
```
"Game [N] selesai! Pemenangnya adalah [nama] dengan [poin] poin!
[Tepuk tangan]
Sekarang kita lanjut ke Game [N+1]..."
```

### Penutup
```
"Itulah 6 game kita hari ini! Pemenang overall dengan total poin terbanyak adalah...
[Umumkan juara]
Tapi ingat — yang terpenting bukan siapa yang menang,
tapi momen kebersamaan kita hari ini!
Sampai gathering berikutnya! 🏡"
```

---

## 🆘 Troubleshooting & Contingency Plan

| Masalah | Solusi |
|---------|--------|
| Error saat masuk lobby | Update ke file terbaru (v3.1), refresh browser |
| SyntaxError di console | Sudah diperbaiki di v3.1 — pastikan pakai file terbaru |
| Supabase auth error / 401 | Sudah diperbaiki di v3.1 — Anon Key JWT sudah diupdate |
| Admin panel error | Update ke file terbaru |
| Foto tidak muncul | Refresh halaman, cek bucket Storage bersifat Public |
| Sing-along tidak muncul | Pastikan peserta ada di halaman tebak lagu |
| Audio tidak bunyi | Tap layar dulu (kebijakan autoplay browser), gunakan Chrome |
| Game tidak bisa dibuka | Admin belum membuka game di Admin Panel |
| MP3 gagal tersimpan | File terlalu besar — kompres dulu (maks 8MB) |
| Proyektor tidak update | Pastikan proyektor.html & game dibuka di domain yang sama |
| CORS error di console | Buka dari domain yang terdaftar di Supabase, atau gunakan localhost |
| Data tidak sinkron | Cek Realtime sudah diaktifkan di Dashboard → Database → Replication |
| Upload foto gagal | Pastikan bucket Public dan policy INSERT sudah ada |
| RLS error | Jalankan ulang bagian SQL yang membuat policies |
| Realtime tidak connect | Firewall mungkin blok WebSocket — fallback polling 5 detik tetap berjalan |
| Rate limit | Tunggu beberapa detik, atau upgrade ke Supabase Pro |
| Internet mati | Pakai file lokal — copy via USB/AirDrop ke HP peserta |
| Proyektor mati | Lanjut main dari HP masing-masing tanpa proyektor |
| Satu peserta tidak bisa masuk | Cek kode room, refresh browser |

---

## 📝 Catatan Teknis

### Cara Kerja Realtime
```
Admin ubah setting → sbUpsert ke Supabase
                  → Supabase Realtime broadcast ke semua subscriber
                  → HP peserta & proyektor auto-update (< 1 detik)

Peserta vote G3   → sbUpsert ke ras_g3_live
                  → Proyektor auto-render hasil vote (tanpa refresh)

Admin play lagu   → sbUpsert ke ras_g6_play_cmd
                  → HP semua peserta detect perubahan
                  → Musik + lirik muncul serentak
```

### Flow Upload Foto (Supabase Storage)
```
Admin upload foto → sbUploadFile('rasmita-photos', 'members/1.jpg', dataUrl)
                 → Dapat URL publik: https://XXXX.supabase.co/storage/v1/object/public/...
                 → Simpan URL ke ras_members.foto_url
                 → Semua HP bisa load dari URL tersebut
```

### Perbedaan dengan Versi Sebelumnya
Versi ini (v5.0) telah **migrasi penuh dari localStorage ke Supabase**:
- Sebelum: foto disimpan sebagai base64 di browser lokal (~5MB limit, 1 device saja)
- Sekarang: foto di Supabase Storage (1GB gratis), URL disimpan di database, semua device sync otomatis

---

## 📋 Changelog

### v3.1 — 28 Maret 2026 (Bug Fix Release)
- ✅ **Fix:** Supabase Anon Key diupdate dari format `sb_publishable_...` ke JWT (`eyJ...`) yang benar — semua koneksi Supabase kini berjalan normal
- ✅ **Fix:** `lirik_singalong` di Game 6 diperbaiki dari regular string ke template literal — tidak lagi menyebabkan SyntaxError
- ✅ **Fix:** Quote escaping di `admin.html` (lines 1179, 1414, 1497, 1504) diperbaiki — fungsi g1RemoveMember & g1AddMember kini berjalan benar
- ✅ **Fix:** Credentials `proyektor.html` diupdate dari placeholder `XXXX` ke URL & key yang aktif
- ✅ **Fix:** Typo `pilihanpilihan` pada data lagu diperbaiki kembali ke `pilihan`

### v3.0 — Maret 2026
- Versi awal dengan 6 game, integrasi Supabase, mode proyektor

---

## ℹ️ Info Event

**Tanggal:** 28 Maret 2026  
**Lokasi:** Wisma Mandiri, Bandung, Jawa Barat  
**Kode Room Default:** FAM2026  
**URL Game:** rasmita-gathering.netlify.app | Short: bit.ly/rasmitas  
**Versi:** v5.0 (Supabase Migration) — Last updated: Maret 2026

---

*Dibuat dengan ❤️ untuk Reuni Keluarga Rasmita 2026 — Gathering & Games · Bandung · Wisma Mandiri*
