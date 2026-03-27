# 🏡 Rasmita's Family Gathering 2026
### Master Documentation — Gathering & Games
> **Versi:** v4.0-sprint3 | **Event:** 28 Maret 2026 | **Venue:** Wisma Mandiri, Bandung

---

## 📁 File-file Utama

| File | Fungsi | Dibuka oleh |
|------|--------|-------------|
| `rasmita-family-gathering-2026.html` | Game utama — landing, lobby, 6 game interaktif | Semua peserta di HP |
| `admin.html` | Admin Panel — upload foto, kontrol game, sing-along | Host/Admin saja |
| `proyektor.html` | Tampilan proyektor — live semua game di layar besar | Laptop → proyektor |
| `SUPABASE-SETUP-ALL-IN-ONE.sql` | Script SQL lengkap inisialisasi database (v4) | Developer / admin teknis |
| `PATCH-v4.0-sprint3.sql` | Patch tabel baru (g2_state, g4_state, gender, results) | Jalankan setelah SQL utama |

---

## ⚙️ Setup Supabase (Sekali Sebelum Event)

### Langkah 1 — Buat Project Supabase
1. Buka **https://supabase.com** → Sign up / Login
2. Klik **"New Project"**, isi:
   - **Name:** `rasmita-2026`
   - **Region:** `Southeast Asia (Singapore)`
3. Catat **Project URL** dan **anon/public key** di Settings → API

### Langkah 2 — Jalankan SQL (Urutan Penting!)
Di **Supabase Dashboard → SQL Editor**, jalankan berurutan:
1. **`SUPABASE-SETUP-ALL-IN-ONE.sql`** — buat semua tabel + RLS + Realtime
2. **`PATCH-v4.0-sprint3.sql`** — tabel baru (g2_state, g4_state, gender, results)
3. **`SUPABASE-STORAGE-POLICY.sql`** — bucket policy untuk upload foto

### Langkah 3 — Buat Storage Buckets
Di **Storage** sidebar, buat 2 bucket:

| Bucket | Public | Isi |
|--------|--------|-----|
| `rasmita-photos` | ✅ | Foto keluarga, profil peserta, foto komedian |
| `rasmita-audio` | ✅ | File MP3 lagu sing-along |

### Langkah 4 — Aktifkan Realtime
Di **Database → Replication**, aktifkan semua tabel dengan tanda ✅ di daftar tabel di bawah.

### Langkah 5 — Isi Credentials di 3 File HTML
```javascript
const SUPABASE_URL = 'https://XXXX.supabase.co';       // ← ganti XXXX
const SUPABASE_ANON_KEY = 'eyJhbGciOiJIUzI1NiIs...';  // ← ganti
```
> Saat ini `rasmita-family-gathering-2026.html` dan `admin.html` sudah berisi credentials aktif (`ykibpxiovrslknagmnpq`). **Proyektor.html** — cek sudah diisi juga.

---

## 🗃️ Struktur Database Supabase (v4.0)

| Tabel | Isi | Realtime |
|-------|-----|----------|
| `ras_event` | Setting event: nama, tanggal, venue, room_code | — |
| `ras_members` | Anggota keluarga + foto_url + **game_tags** | — |
| `ras_players` | Peserta join + photo_url + **gender** | ✅ |
| `ras_open_games` | Game yang dibuka admin (array game_ids) | ✅ |
| `ras_g1_state` | State Memory Match: kartu, giliran, skor tim | ✅ |
| `ras_g1_teams` | Setup tim Game 1 | ✅ |
| `ras_g1_scores` | Skor final Memory Match | ✅ |
| `ras_g2_state` | **[v4]** State Game 2 sinkron: foto aktif, stage blur, jawaban | ✅ |
| `ras_g3_questions` | Pertanyaan custom Game 3 | — |
| `ras_g3_live` | Data voting live → proyektor | ✅ |
| `ras_g4_state` | **[v4]** State Game 4: ronde aktif, foto per ronde, timer | ✅ |
| `ras_g4_scores` | Skor Timeline per peserta | ✅ |
| `ras_g5_results` | Hasil Mirip Siapa per peserta → proyektor | ✅ |
| `ras_g6_singalong` | Status sing-along → proyektor + HP | ✅ |
| `ras_g6_play_cmd` | Perintah play dari admin → HP peserta | ✅ |
| `ras_lagu_storage` | URL MP3 + lirik per lagu | — |
| `ras_komedian_photos` | Foto komedian (URL dari Storage) | — |
| `ras_results_summary` | **[v4]** Rekap skor total semua game per peserta | ✅ |

### Batas Gratis Supabase Free Plan

| Resource | Batas | Cukup untuk |
|----------|-------|-------------|
| Database | 500 MB | ✅ Gathering 100 orang |
| Storage | 1 GB | ✅ Semua foto keluarga |
| Bandwidth | 2 GB/bulan | ✅ 1 hari event |
| Realtime connections | 200 concurrent | ✅ |

---

## 🚀 Cara Mulai

### Mode Online (Rekomendasi)
```
1. Setup Supabase (langkah di atas)
2. Upload 3 file HTML + SQL ke Netlify (drag & drop, gratis)
3. Dapatkan URL publik → share ke semua peserta via WhatsApp
4. Admin buka admin.html | Proyektor buka proyektor.html
```

### Mode Offline
```
1. Buka admin.html di browser laptop
2. Upload foto keluarga, set kode room
3. Hubungkan laptop ke proyektor via HDMI → proyektor.html → F11
4. Peserta buka rasmita-family-gathering-2026.html di HP
```
> ⚠️ Mode offline: Supabase tetap butuh internet untuk sync realtime.

---

## 📽️ Setup Proyektor / TV

```
[Laptop Admin]  ──HDMI──▶ [Proyektor/TV]  ← proyektor.html fullscreen (F11)
[HP Peserta]    ──WiFi──▶ [URL Game]       ← tiap orang main sendiri
[Browser Admin] ────────▶ admin.html       ← kontrol semua game
```

### Screen List Proyektor (v4.0)

| Screen ID | Ditampilkan saat | Konten |
|-----------|-----------------|--------|
| `screenWelcome` | Default pertama buka | Nama event, URL, QR code, daftar game |
| `screenLobby` | Semua game tertutup | Daftar peserta live |
| `screenGame1` | Admin buka Game 1 | Grid kartu Memory Match + leaderboard tim |
| `screenGame2` | **[v4]** Admin buka Game 2 | Foto blur→jelas + counter jawaban |
| `screenGame3` | Admin buka Game 3 | Hasil voting real-time per pertanyaan |
| `screenGame4` | Admin buka Game 4 | **[v4]** 5 foto per ronde + timer + leaderboard |
| `screenGame5` | Admin buka Game 5 | Grid hasil komedian semua peserta |
| `screenGame6` | Admin buka Game 6 | Lirik sing-along besar |
| `screenResult` | **[v4]** Admin klik "Tampilkan Hasil" | Podium 🥇🥈🥉 + rekap skor semua game |

### Auto-Switch Logic
```
ras_open_games berubah → proyektor otomatis pindah screen:
  game_ids kosong    → screenLobby
  game_ids = [1]     → screenGame1
  game_ids = [1, 2]  → screenGame2  (game paling baru)
  game_ids = [1,2,3] → screenGame3  (game paling baru)
```
> Saat proyektor pertama dibuka, langsung cek game mana yang sudah dibuka admin dan auto-switch ke sana.

---

## 🎮 6 Game — Spesifikasi Lengkap

| # | Game | Mode | Sinkron | Durasi |
|---|------|------|---------|--------|
| 🃏 1 | Memory Match Foto | Tim (4–5 tim) | ✅ Realtime | 15 mnt |
| 🔍 2 | Tebak Siapa? | Semua individual | ✅ **[v4]** Sinkron 1 foto | 20 mnt |
| 🗳️ 3 | Siapa yang Paling...? | Semua serentak | ✅ Voting live | 15 mnt |
| ⏳ 4 | Timeline Keluarga | Semua individual | ✅ **[v4]** Per ronde | 15 mnt |
| 🎭 5 | Kamu Mirip Siapa? | Individual bebas | ❌ | ~5 mnt/orang |
| 🎵 6 | Tebak Lagu Indonesia | Semua individual | ✅ Per lagu | 10 mnt |

---

## 🃏 GAME 1 — Memory Match Foto

**Pemain:** 4–5 tim (admin pilih dari peserta) | **Durasi:** 15 menit

**Setup Tim di Admin Panel:**
- Admin Panel → Game 1 → pilih peserta untuk setiap tim
- Bisa 2–5 tim, masing-masing tim main bergantian dari 1 HP tim

**Sistem Poin:**

| Kondisi | Poin |
|---------|------|
| Pasangan cocok | +10 |
| Tidak cocok | 0 |

**Foto:** Ambil dari `ras_members` dengan `game_tags` berisi `'g1'`. Assign di Admin → Foto Keluarga → Tab Memory Match. **15 foto = 30 kartu (15 pasang).**

**Script MC:**
> *"Ingat-ingat posisi kartu yang sudah dibuka! Itu kunci menang. 4–5 tim bergantian, 1 HP per tim."*

---

## 🔍 GAME 2 — Tebak Siapa? (v4: Sinkron)

**Pemain:** Semua, 1 HP per orang | **Durasi:** 20 menit | **Foto:** 5

**Flow Sinkron (v4):**
1. Admin pilih 5 foto di Admin Panel → klik "Mulai Game 2"
2. Semua HP peserta tampilkan foto yang **sama** secara bersamaan
3. Foto muncul bertahap: stage 0 (paling blur) → stage 5 (jelas)
4. Peserta ketik jawaban di HP → submit
5. Proyektor tampilkan foto blur besar + counter "X sudah menjawab"
6. Admin klik "Next" atau otomatis setelah semua jawab

**Sistem Poin (Menurun per Stage):**

| Stage | Kondisi | Poin |
|-------|---------|------|
| 0–1 | Sangat awal (masih blur) | 400–500 |
| 2–3 | Cukup awal | 250–400 |
| 4 | Sebagian terlihat | 150–250 |
| 5 | Foto jelas | 100 |
| — | Salah | 0 |

**Foto:** `game_tags` berisi `'g2'`. Assign di Admin → Foto Keluarga → Tab Tebak Siapa. **5 foto.**

**Script MC:**
> *"Semua lihat foto yang sama! Siapa yang paling cepat tebak dari blur, dapat poin paling besar!"*

---

## 🗳️ GAME 3 — Siapa yang Paling...?

**Pemain:** Semua serentak | **Durasi:** 15 menit | **Timer per pertanyaan:** 30 detik

**Sistem Poin:**

| Kondisi | Poin |
|---------|------|
| Dipilih oleh pemain lain | +5 per suara |
| Pilih orang yang paling banyak dipilih | +10 |

**10 Pertanyaan Default:**
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

**Edit Pertanyaan:** Admin Panel → Game 3 → Tab ❓ Edit Pertanyaan → Simpan.

**Foto:** `game_tags` berisi `'g3'`. **20 foto** = 20 kandidat voting.

**Script MC:**
> *"Jujur ya! Kalau ada yang tersinggung... itu berarti semua sepakat! Ini bukan celaan, ini sayang! 😂"*

---

## ⏳ GAME 4 — Timeline Keluarga (v4: Per Ronde + Timer)

**Pemain:** Semua | **Durasi:** 15 menit | **Ronde:** 3–5 ronde × 5 foto per ronde

**Flow Ronde (v4):**
1. Admin setup ronde di Admin Panel (pilih jumlah ronde, auto-assign atau manual)
2. Klik "Mulai Ronde 1" → 5 foto tampil di proyektor + HP peserta
3. Timer 60 detik berjalan
4. Peserta drag & drop urutan usia (termuda → tertua) di HP
5. Timer habis → semua submit otomatis → skor dihitung
6. Proyektor tampilkan jawaban benar + delta skor
7. Admin klik "Ronde Berikutnya" (atau otomatis 10 detik)

**Sistem Poin:**

| Kondisi | Poin |
|---------|------|
| 5/5 benar | +100 |
| 4/5 benar | +70 |
| 3/5 benar | +40 |
| < 3 benar | 0 |
| Bonus kecepatan < 10 detik | +50 |
| Bonus kecepatan 10–20 detik | +25 |

**Foto:** `game_tags` berisi `'g4'`. Setup di Admin → Foto Keluarga → Tab Timeline. 5 foto per ronde, jumlah ronde menyesuaikan foto tersedia (misal 20 foto = 4 ronde).

**Script MC:**
> *"Kamu tau usia pasti semua anggota keluarga ini? Sekarang buktikan! Kalau salah, berarti kurang perhatian! 😄"*

---

## 🎭 GAME 5 — Kamu Mirip Siapa? (v4: Filter Gender)

**Pemain:** Individual | **Durasi:** ~5 menit per orang | **Database:** 100 komedian

**Registrasi (v4):** Saat masuk lobby, peserta pilih **jenis kelamin (👨 Pria / 👩 Wanita)** → hasil komedian disesuaikan gender.

**6 Pertanyaan:** Jenis kelamin → Kisaran usia → Postur → Rambut → Ciri wajah → Karakter humor

**Database Komedian:**

| Kategori | Jumlah | Rentang ID |
|----------|--------|------------|
| Komedian Pria | ~50 | ID 1–75 |
| Komedian Wanita | 25 | ID 101–125 |

**Komedian Terbaru (v4 — penggantian yang tidak terkenal):**

| ID | Lama | Baru |
|----|------|------|
| 60 | Kiky Saputra (pria) | **Cak Lontong** |
| 67 | Oken | **Boris Bokir** |
| 70 | Pak Besut | **Abdur Arsyad** |
| 72 | Cagur | **Pepen** |
| 74 | Ical Residivis | **Tessy** |
| 75 | Al Kausar | **Ucok Baba** |
| 118 | Nora Alexandra | **Yati Surachman** |
| 121 | Shinta Bachir | **Nurul Arifin** |
| 124 | Nita Thalia | **Nining Meida** |
| 125 | Rohimah | **Mpok Atiek** |

**Script MC:**
> *"Yang dapat Nunung, Sule, atau Raditya Dika — selamat ditakdirkan jadi bintang! Yang dapat komedian yang nggak kalian kenal... kalian unik banget sampai algoritma bingung! 😂"*

---

## 🎵 GAME 6 — Tebak Lagu Indonesia

**Pemain:** Semua | **Durasi:** 10 menit | **5 Lagu lintas generasi**

| # | Judul | Artis | Tahun |
|---|-------|-------|-------|
| 1 | 🌴 Tabola Bale | Silet Open Up feat. Jacson Zeran | 2025 |
| 2 | 🏡 Harta yang Paling Berharga | Hedi Yunus (OST Keluarga Cemara) | 1996 |
| 3 | 💃 Dansa Yok Dansa | Harvey Malaiholo | 1985 |
| 4 | ☕ Kopi Dangdut | Fahmi Shahab | 1987 |
| 5 | 🕺 Goyang Dumang | Cita Citata | 2014 |

**Sistem Poin:**

| Kondisi | Poin |
|---------|------|
| Benar tanpa petunjuk | 100 |
| Benar dengan petunjuk | 50 |
| Salah | 0 |
| **Maksimum** | **500** |

**Upload MP3:** Admin Panel → 🎵 Lagu (Game 6) → upload MP3 chorus per lagu (maks 8MB). Tanpa MP3: pakai melodi sintetis Web Audio API.

**Alur Sing-Along:**
```
Peserta tebak → admin klik ▶ Play → musik + lirik muncul di semua HP
→ lirik BESAR tampil di proyektor → semua nyanyi bareng!
```

---

## 📸 Upload Foto Keluarga per Game

Di Admin Panel → **📸 Foto Keluarga** ada 5 tab:

| Tab | Keterangan | Jumlah Foto |
|-----|------------|-------------|
| Semua Foto | Upload + kelola semua foto | Bebas |
| 🃏 Memory Match | Assign foto untuk Game 1 | **15 foto** |
| 🔍 Tebak Siapa | Assign foto untuk Game 2 | **5 foto** |
| 🗳️ Siapa Paling | Assign foto untuk Game 3 | **20 foto** |
| ⏳ Timeline | Assign foto + input urutan usia | **5 × N ronde** |

**Cara Assign Foto ke Game:**
1. Upload semua foto di tab **Semua Foto**
2. Buka tab game yang diinginkan → centang/klik foto yang akan dipakai
3. Klik **💾 Simpan** — langsung aktif di game

> ⚠️ **Penting:** Setelah upload foto, pastikan klik tombol **Simpan** di tab game agar `game_tags` tersimpan ke Supabase.

**Panduan Foto:**
- Format: JPG atau PNG, ukuran ≥ 300×300 px
- Ekspresi: wajah jelas, terang, background polos lebih baik
- Kumpulkan via grup WhatsApp, minta selfie portrait terbaru

---

## 🏆 Sistem Hadiah (Rekomendasi)

| Kategori | Hadiah |
|----------|--------|
| 🥇 Juara Overall (total semua game) | Hadiah utama |
| Hall of Fame Game 3 (paling sering dipilih) | Piala lucu / doorprize 😄 |
| Game 5: dapat komedian teraneh | Hadiah kejutan |
| Game 6: nyanyi paling keras | Hadiah partisipasi 🎤 |

---

## 🚀 Deploy & Publish

### Netlify (Paling Mudah, Gratis) ✅
```
1. netlify.com → drag & drop seluruh folder project
2. (Opsional) Site settings → "Change site name" → rasmita-gathering-2026
3. URL: https://rasmita-gathering-2026.netlify.app
```

### GitHub Pages
```
1. github.com → New repo → rasmita-gathering-2026 (Public)
2. Upload file HTML → Settings → Pages → Deploy from main
3. URL: https://USERNAME.github.io/rasmita-gathering-2026
```

### Vercel
```
vercel.com → Upload folder → Deploy
URL: https://rasmita-gathering-2026.vercel.app
```

### QR Code
```
qrcode-monkey.com → paste URL
Warna: Foreground #2D5016, Background #FDF8EE
Download PNG → print minimal 10×10 cm
```

### Short URL: `bit.ly/rasmitas` (sudah aktif di QR)

---

## ✅ Checklist Persiapan

### H-7
- [ ] Jalankan `SUPABASE-SETUP-ALL-IN-ONE.sql` di SQL Editor
- [ ] Jalankan `PATCH-v4.0-sprint3.sql`
- [ ] Jalankan `SUPABASE-STORAGE-POLICY.sql`
- [ ] Test buka 3 file HTML di Chrome/Edge — tidak ada error di Console
- [ ] Kumpulkan foto anggota keluarga (min 15, ideal 25)

### H-3
- [ ] Upload semua foto di Admin → Foto Keluarga → Semua Foto
- [ ] Assign foto ke tiap game (Memory Match 15, Tebak Siapa 5, Siapa Paling 20, Timeline per ronde)
- [ ] Set nama event, tanggal, lokasi, kode room di Admin → Pengaturan Event
- [ ] Upload foto komedian (opsional, ada fallback emoji 🎭)
- [ ] Upload ke Netlify, test URL dari HP
- [ ] Siapkan MP3 lagu (opsional)

### H-1
- [ ] Test seluruh 6 game end-to-end
- [ ] Test proyektor auto-switch (buka game di admin → cek proyektor pindah screen)
- [ ] Test screen Result/Pemenang
- [ ] Cek kabel HDMI + adapter + speaker eksternal
- [ ] Backup folder ke flashdisk

### Hari H — 30 Menit Sebelum
- [ ] Buka `proyektor.html` di laptop → **F11 fullscreen** → tampil di layar besar
- [ ] Buka `admin.html` di tab lain atau HP admin
- [ ] Pastikan peserta bisa scan QR / buka URL
- [ ] **JANGAN buka semua game sekaligus** — buka satu per satu sesuai giliran
- [ ] Test audio Game 6 — tap layar dulu jika tidak bunyi (autoplay policy browser)

---

## 📅 Urutan Acara (Rekomendasi)

```
[Jam 1] Registrasi peserta → masuk room → upload foto profil → pilih gender
[Jam 2] Game 1: Memory Match Foto (hangat-hangat, main per tim)
[Jam 3] Game 2: Tebak Siapa? (sinkron, semua lihat foto yang sama)
[Jam 4] Game 3: Siapa yang Paling...? (voting seru, tampilkan di proyektor!)
[Jeda]  Makan / break
[Jam 5] Game 4: Timeline Keluarga (per ronde, admin kontrol)
[Jam 6] Game 5: Kamu Mirip Siapa? (individual, share hasil ke grup)
[Jam 7] Game 6: Tebak Lagu + Sing-Along (penutup paling meriah!)
[Jam 8] Admin klik "Tampilkan Hasil" → Proyektor tampil Podium Pemenang 🎉
```

---

## 🎤 Script MC — Alur Lengkap

### Pembuka
```
"Halo semua! Selamat datang di Rasmita Family Gathering 2026!
Kita punya 6 game seru yang bisa dimainkan dari HP kalian.

Cara bergabung:
1. Buka browser di HP
2. Ketik: rasmita-gathering.netlify.app (atau scan QR code)
3. Masukkan nama + pilih jenis kelamin
4. Foto profil boleh diupload
5. Klik 'Masuk ke Lobby'

[Tunggu semua peserta masuk]
Oke, semua sudah masuk? Kita mulai dari Game 1..."
```

### Transisi Antar Game
```
"Game [N] selesai! Pemenangnya [nama] dengan [poin] poin! 👏
Sekarang kita lanjut ke Game [N+1]..."
```

### Penutup
```
"Itulah 6 game kita hari ini!
[Admin klik Tampilkan Hasil → proyektor tampilkan podium]
Pemenang overall adalah... [nama] dengan [poin] poin total!
Tapi yang terpenting — momen kebersamaan kita hari ini!
Sampai gathering berikutnya! 🏡"
```

---

## 🆘 Troubleshooting

| Masalah | Penyebab | Solusi |
|---------|----------|--------|
| Tidak bisa login (SyntaxError) | Broken string di JS | Update ke file v4.0-sprint3 terbaru |
| Foto tidak muncul di tab game | `game_tags` kolom belum ada di DB | Jalankan `SUPABASE-SETUP-ALL-IN-ONE.sql` ulang |
| Upload foto 400 Bad Request | Bucket Storage belum ada policy | Jalankan `SUPABASE-STORAGE-POLICY.sql` |
| Proyektor blank / crash | SyntaxError di proyektor.html | Update ke file v4.0-sprint3 terbaru |
| Proyektor tidak auto-switch | Script crash sebelum startRealtime() | Update ke file v4.0-sprint3 (sudah fix) |
| Duplikat pertanyaan Game 3 | Bug lama | Sudah fix di v4 |
| Game 2 tidak sinkron | `ras_g2_state` belum dibuat | Jalankan `PATCH-v4.0-sprint3.sql` |
| Game 4 tidak tampil foto | `ras_g4_state` belum dibuat | Jalankan `PATCH-v4.0-sprint3.sql` |
| Komedian tidak sesuai gender | Peserta tidak pilih gender saat masuk | Info peserta untuk refresh + pilih gender |
| Audio tidak bunyi | Autoplay policy browser | Tap/klik layar dulu, gunakan Chrome |
| Realtime tidak jalan | Tabel belum diaktifkan di Replication | Supabase → Database → Replication → aktifkan tabel |
| Internet mati | — | Pakai file lokal, copy via USB ke HP peserta |
| Proyektor mati | — | Lanjut main dari HP masing-masing |

---

## 📋 Changelog

### v4.0-sprint3 (Current)
- 🔧 **Fix:** SyntaxError `font-family:'Playfair Display'` di JS string (game + proyektor)
- 🔧 **Fix:** `game_tags` tidak tersimpan saat upload foto → tab game selalu kosong
- 🔧 **Fix:** Filter foto per game sekarang fallback ke semua foto jika `game_tags` kosong
- 🔧 **Fix:** Proyektor tidak auto-switch ke game yang sudah dibuka saat pertama dibuka
- ✨ **Baru:** Initial auto-switch proyektor saat pertama dibuka (cek game aktif langsung)

### v4.0-sprint2
- ✨ Game 2 sinkron — semua peserta lihat foto yang sama bersamaan
- ✨ Screen Game 2 di proyektor (foto blur→jelas + counter jawaban)
- ✨ Game 4 per ronde + timer (admin klik mulai atau auto timer)
- ✨ Screen Game 4 upgrade (5 foto per ronde tampil di proyektor)
- ✨ Proyektor auto-switch mengikuti game yang dibuka admin
- ✨ Screen Result/Pemenang — podium + rekap skor semua game
- ✨ Game 5 filter komedian sesuai gender peserta
- ✨ Ganti 10 komedian yang tidak terkenal dengan nama lebih populer
- ✨ Kolom gender di form registrasi peserta
- ✨ Tabel ras_g2_state, ras_g4_state, ras_results_summary

### v3.2
- Fix SyntaxError lirik sing-along (raw newlines di JS string)
- Fix credentials proyektor.html (masih placeholder XXXX)
- Fix upload foto 400 Bad Request (Storage bucket policy)
- Fix SQL Komedian syntax error (koma salah posisi)

### v3.0
- Versi awal: 6 game, integrasi Supabase, mode proyektor

---

## ℹ️ Info Event

**Tanggal:** 28 Maret 2026  
**Lokasi:** Wisma Mandiri, Bandung, Jawa Barat  
**Kode Room:** FAM2026  
**URL Game:** rasmita-gathering.netlify.app  
**Short URL:** bit.ly/rasmitas  
**Supabase Project:** ykibpxiovrslknagmnpq.supabase.co

---

*Dibuat dengan ❤️ untuk Reuni Keluarga Rasmita 2026 — Gathering & Games · Bandung · Wisma Mandiri*

---

## 🔧 Fix Log — v4.0-sprint4

### Masalah yang Diperbaiki

#### 1. SQL — Tidak Ada Perubahan
Tidak ada tabel baru yang dibutuhkan. Pastikan sudah jalankan berurutan:
1. `SUPABASE-SETUP-ALL-IN-ONE.sql`
2. `PATCH-v4.0-sprint3.sql`
3. `SUPABASE-STORAGE-POLICY.sql`

#### 2. Setelah Game Selesai — Tidak Bisa Kembali ke Pilihan Game
**Root cause:** Semua screen hasil game (Game 1–5) hanya punya tombol "Main Lagi", tidak ada tombol kembali ke lobby.
**Fix:** Ditambahkan tombol **"← Kembali ke Lobby"** di screen hasil semua 6 game.

#### 3. Game 3–6 Tidak Ada Tombol Mulai
**Root cause:** `initGame3()` langsung load soal pertama, `initGame4()` langsung load ronde 1, `initGame5()` langsung tampil quiz, `initGame6()` langsung play lagu — tidak ada screen perantara.
**Fix:** Semua game sekarang memiliki **"Waiting Screen"** saat pertama masuk:
- Menampilkan deskripsi singkat game
- Tombol **▶ Mulai Game!** → baru masuk ke gameplay
- Tombol **← Kembali ke Lobby** untuk keluar

#### 4. User Tidak Bisa Bergabung Setelah Game Dimulai
**Root cause (3 lapis):**
1. `showLobby()` menggunakan `state.openGames` yang mungkin stale (tidak fresh dari DB)
2. Polling `setInterval` hanya jalan jika `currentPage === 'lobby'` — kalau user di halaman game, tidak ada update
3. `joinRoom()` tidak cek apakah sudah ada game aktif setelah masuk

**Fix:**
- `showLobby()` sekarang **async** — selalu fetch `ras_open_games` dari Supabase sebelum render
- Polling **selalu aktif** (tidak peduli user di halaman mana)
- `joinRoom()` setelah masuk lobby: jika ada 1 game aktif → **auto-enter** setelah 1.5 detik dengan notifikasi
- Realtime subscription `ras_open_games`: jika admin **membuka** game baru dan user di lobby → auto-enter; jika admin **menutup** game dan user sedang di dalam → auto-kembali ke lobby dengan notifikasi

### Alur Baru User Flow

```
User buka URL → isi nama & gender → Masuk ke Lobby

[Skenario A: Masuk sebelum game dibuka]
Lobby → menunggu → admin buka game → kartu game aktif
→ User tap game → Wait Screen ("▶ Mulai Game!")
→ Tap Mulai → Gameplay → Selesai → "← Kembali ke Lobby"

[Skenario B: Masuk saat game sudah aktif]
Lobby → langsung terdeteksi ada game aktif
→ Notif "🎮 [Game X] sudah aktif! Masuk otomatis..."
→ Auto-masuk ke Wait Screen → Tap Mulai → Gameplay

[Skenario C: Admin buka game saat user di lobby]
Lobby → realtime/polling detect perubahan
→ Notif "🎮 Host membuka [Game X]!"
→ Auto-enter (jika hanya 1 game terbuka)

[Skenario D: Admin tutup game saat user sedang main]
User di dalam game → polling detect game ditutup
→ Notif "⏹ Game ditutup oleh host. Kembali ke lobby..."
→ Auto-kembali ke lobby setelah 2 detik
```
