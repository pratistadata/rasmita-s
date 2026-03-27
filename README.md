# 🏡 Rasmita's Family Gathering 2026
### Aplikasi Game Interaktif Reuni Keluarga — Gathering & Games

> **Single-file web app** — tidak perlu server, tidak perlu install. Buka browser, langsung main!

---

## File-file Utama

| File | Fungsi | Dibuka oleh |
|------|--------|-------------|
| `rasmita-family-gathering-2026.html` | **Game utama** | Semua peserta di HP |
| `admin.html` | **Admin Panel** — upload foto, setting, kontrol game, sing-along | Host/Admin saja |
| `proyektor.html` | **Tampilan proyektor** — live semua game di layar besar | Laptop terhubung proyektor |

---

## Cara Cepat Mulai

### Offline (Tanpa Internet)
```
1. Buka admin.html di browser laptop
2. Upload foto anggota keluarga (min 10 foto)
3. Set kode room di Pengaturan Event
4. Hubungkan laptop ke proyektor via HDMI
5. Buka proyektor.html → F11 fullscreen → tampil di layar besar
6. Peserta buka rasmita-family-gathering-2026.html di HP
7. Buka game satu per satu dari Admin Panel
```

### Online (Rekomendasi)
```
1. Upload seluruh folder ke Netlify / Vercel (drag & drop, gratis)
2. Dapatkan URL publik
3. Share URL ke semua peserta via WhatsApp
4. Admin buka admin.html di URL yang sama
5. Proyektor buka proyektor.html
```

---

## Setup Proyektor / TV

```
[Laptop Admin]   ──HDMI──> [Proyektor/TV]  ← proyektor.html fullscreen
[HP Peserta]     ──WiFi──> [URL Game]       ← tiap orang main sendiri
[Browser Admin]  ──────> admin.html         ← kontrol semua game
```

**Proyektor otomatis menampilkan (refresh tiap 2 detik):**
- 🏠 Welcome screen + peserta yang join
- 👥 Semua peserta live
- 🃏 Leaderboard Memory Match
- 🗳️ Hasil voting Siapa Paling real-time
- ⏳ Leaderboard Timeline
- 🎭 Hasil Mirip Siapa per peserta
- 🎵 Lirik sing-along BESAR saat Tebak Lagu

---

## 6 Game yang Tersedia

| # | Game | Pemain | Durasi | Perlu Foto |
|---|------|--------|--------|------------|
| 🃏 1 | Memory Match Foto | **2–4 dipilih admin** | 15 mnt | ✅ Ya |
| 🔍 2 | Tebak Siapa? | Semua | 20 mnt | ✅ Ya |
| 🗳️ 3 | Siapa yang Paling...? | Semua | 15 mnt | ✅ Ya |
| ⏳ 4 | Timeline Keluarga | Semua | 15 mnt | ✅ Ya |
| 🎭 5 | Kamu Mirip Siapa? | Individual | 5 mnt | ❌ Tidak |
| 🎵 6 | Tebak Lagu Indonesia | Semua | 10 mnt | ❌ Tidak |

---

## Upload Foto Keluarga

**Admin Panel → 📸 Foto Keluarga:**

| Tab | Keterangan |
|-----|------------|
| Semua Foto | Upload & kelola semua foto di sini |
| Memory Match | Preview foto untuk Game 1 |
| Tebak Siapa | Preview foto untuk Game 2 |
| Siapa Paling | Preview foto kandidat voting Game 3 |
| Timeline | Preview foto untuk Game 4 |

> Upload minimal **10 foto**, ideal **15–20 foto**. Semua game otomatis menggunakan foto yang sama.

---

## Game 1 — Memory Match: Pemilihan Pemain

Saat masuk Game 1, muncul layar **Pilih Pemain**:
- Peserta yang sudah join otomatis muncul sebagai pilihan
- Pilih 1–4 pemain yang akan bermain
- Bisa tambah nama manual dengan tombol **+ Tambah**
- Klik **▶ Mulai Game!**

---

## Game 3 — Edit Pertanyaan

**Admin Panel → 🗳️ Game 3 → Tab ❓ Edit Pertanyaan:**
- Edit teks pertanyaan langsung di admin
- Tambah / hapus pertanyaan
- Reset ke 15 pertanyaan default
- Klik **💾 Simpan** — langsung aktif di game peserta

---

## Game 6 — Tebak Lagu & Sing-Along

### 5 Lagu:
| # | Judul | Artis | Tahun |
|---|-------|-------|-------|
| 1 | 🌴 Tabola Bale | Silet Open Up feat. Jacson Zeran | 2025 |
| 2 | 🏡 Harta yang Paling Berharga | Hedi Yunus (OST Keluarga Cemara) | 1996 |
| 3 | 💃 Dansa Yok Dansa | Harvey Malaiholo | 1985 |
| 4 | ☕ Kopi Dangdut | Fahmi Shahab | 1987 |
| 5 | 🕺 Goyang Dumang | Cita Citata | 2014 |

### Upload MP3 Chorus
1. Admin Panel → **🎵 Lagu (Game 6)**
2. Upload MP3 bagian chorus per lagu (maks 8MB)
3. Edit lirik sing-along langsung di kolom teks
4. Klik **💾 Simpan Lirik**

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

> Tanpa MP3: otomatis menggunakan melodi sintetis (Web Audio API)

---

## Scraping Foto Komedian (Game 5)

```bash
pip install requests beautifulsoup4 Pillow
python3 scrape_komedian.py
# Hasil: komedian_photos/01-sule.jpg, dst.
```
Upload ke `admin.html` → **🎭 Komedian** → Upload Foto Massal

---

## localStorage Keys (untuk Developer)

| Key | Isi |
|-----|-----|
| `ras_event` | Setting event (nama, tanggal, venue, room code) |
| `ras_members` | Data anggota keluarga + foto (base64) |
| `ras_players` | Peserta yang sudah join |
| `ras_open_games` | Game yang dibuka admin |
| `ras_g3_questions` | Pertanyaan custom Game 3 |
| `ras_lagu_storage` | MP3 + lirik custom per lagu |
| `ras_lagu_lirik` | Lirik per lagu (sync ke HP peserta) |
| `ras_g3_live` | Data voting live (→ proyektor) |
| `ras_g6_singalong` | Status sing-along (→ proyektor + HP) |
| `ras_g6_play_cmd` | Perintah play dari admin (→ HP peserta) |
| `ras_g1_scores` | Skor Memory Match (→ proyektor) |
| `ras_g4_scores` | Skor Timeline (→ proyektor) |
| `ras_g5_results` | Hasil Mirip Siapa (→ proyektor) |
| `ras_komedian_photos` | Foto komedian (base64) |

---

## Troubleshooting

| Masalah | Solusi |
|---------|--------|
| Error saat masuk lobby | Sudah diperbaiki — update ke file terbaru |
| Admin panel error | Sudah diperbaiki — update ke file terbaru |
| Foto tidak muncul | Refresh halaman, cek storage browser |
| Sing-along tidak muncul | Pastikan peserta ada di halaman tebak lagu |
| Audio tidak bunyi | Tap layar dulu (kebijakan autoplay browser) |
| Game tidak bisa dibuka | Admin belum membuka game di Admin Panel |
| MP3 gagal tersimpan | File terlalu besar — kompres dulu (maks 8MB) |
| Proyektor tidak update | Pastikan proyektor.html & game dibuka di domain yang sama |

---

## Catatan Penting

- Semua data tersimpan di **localStorage browser** — jangan ganti browser atau device saat event
- Foto disimpan sebagai base64 — ukuran ideal per foto < 500KB
- Proyektor, admin, dan game **harus dibuka di domain/file yang sama** agar localStorage terbaca
- Untuk hosting online: gunakan 1 URL yang sama untuk semua file

---

*Dibuat dengan ❤️ untuk Reuni Keluarga Rasmita 2026*  
*Gathering & Games · Bandung · Wisma Mandiri*  
*Last updated: Maret 2026 — v3.0*
