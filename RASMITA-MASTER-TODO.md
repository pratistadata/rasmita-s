# 🏡 Rasmita's Family Gathering 2026
### Master Documentation & Development TODO
> **Versi:** v4.0-dev | **Event:** 28 Maret 2026 | **Venue:** Wisma Mandiri, Bandung

---

## 📁 File-file Utama

| File | Fungsi | Dibuka oleh |
|------|--------|-------------|
| `rasmita-family-gathering-2026.html` | Game utama — landing, lobby, 6 game interaktif | Semua peserta di HP |
| `admin.html` | Admin Panel — upload foto, kontrol game, sing-along | Host/Admin saja |
| `proyektor.html` | Tampilan proyektor — live semua game di layar besar | Laptop → proyektor |
| `SUPABASE-SETUP-ALL-IN-ONE.sql` | Script SQL lengkap inisialisasi database | Developer / admin teknis |

---

## 🗃️ Struktur Database Supabase (v4.0)

| Tabel | Isi | Realtime |
|-------|-----|----------|
| `ras_event` | Setting event: nama, tanggal, venue, room_code | — |
| `ras_members` | Anggota keluarga + foto_url + **game_tags** | — |
| `ras_players` | Peserta join + photo_url + gender | ✅ |
| `ras_open_games` | Game yang dibuka admin (array game_ids) | ✅ |
| `ras_g2_state` | **[BARU]** State Game 2 sinkron: foto aktif, timer, jawaban | ✅ |
| `ras_g3_questions` | Pertanyaan custom Game 3 | — |
| `ras_g3_live` | Data voting live → proyektor | ✅ |
| `ras_g4_state` | **[BARU]** State Game 4: ronde aktif, foto per ronde, timer | ✅ |
| `ras_g4_scores` | Skor Timeline per peserta → proyektor | ✅ |
| `ras_g5_results` | Hasil Mirip Siapa per peserta → proyektor | ✅ |
| `ras_g6_singalong` | Status sing-along → proyektor + HP | ✅ |
| `ras_g6_play_cmd` | Perintah play dari admin → HP peserta | ✅ |
| `ras_g1_state` | State Memory Match: kartu, giliran, skor tim | ✅ |
| `ras_g1_teams` | Setup tim Game 1 | ✅ |
| `ras_g1_scores` | Skor final Memory Match | ✅ |
| `ras_lagu_storage` | URL MP3 + lirik per lagu | — |
| `ras_komedian_photos` | Foto komedian (URL dari Storage) | — |
| `ras_results_summary` | **[BARU]** Rekap skor total semua game per peserta | ✅ |

---

## 🎮 6 Game — Spesifikasi Lengkap

| # | Game | Mode | Sinkron | Proyektor |
|---|------|------|---------|-----------|
| 🃏 1 | Memory Match Foto | Tim (4–5 tim, 1 HP/tim) | ✅ Realtime | Grid kartu + leaderboard tim |
| 🔍 2 | Tebak Siapa? | Semua individual | ✅ Sinkron 1 foto | **[BARU]** Foto blur→jelas di layar besar |
| 🗳️ 3 | Siapa yang Paling...? | Semua serentak | ✅ Voting live | Hasil voting real-time |
| ⏳ 4 | Timeline Keluarga | Semua individual | ✅ Per ronde | **[BARU]** 5 foto per ronde tampil di proyektor |
| 🎭 5 | Kamu Mirip Siapa? | Individual | ❌ Bebas | Grid hasil semua peserta |
| 🎵 6 | Tebak Lagu Indonesia | Semua individual | ✅ Per lagu | Lirik sing-along besar |

---

## 📋 TODO LIST — Bug Fixes & Fitur Baru

### 🔴 BUG FIXES (Kerjakan Pertama)

#### BUG-1: `game_tags` tidak ter-load dari Supabase
**File:** `admin.html`
**Lokasi:** fungsi `loadAllFromSupabase()` baris ~1136
**Problem:** Saat mapping `memberRows` ke `FAMILY_MEMBERS`, properti `game_tags` tidak di-assign.
```javascript
// SEKARANG (salah):
FAMILY_MEMBERS = memberRows.map(r => ({ id: r.member_id, nama: r.nama, foto: r.foto_url || '' }));

// FIX:
FAMILY_MEMBERS = memberRows.map(r => ({
  id: r.member_id,
  nama: r.nama,
  foto: r.foto_url || '',
  game_tags: r.game_tags || ['g1','g2','g3','g4']  // ← tambah ini
}));
```
**Efek bug:** Tab Timeline/Memory Match/dll di Foto Keluarga tampil kosong meski foto sudah ada.

---

#### BUG-2: SyntaxError `proyektor.html` line 695
**File:** `proyektor.html`
**Lokasi:** fungsi `buildNavTabs()`
**Problem:** Quote `'` dan `"` saling konflik di string innerHTML.
```javascript
// SEKARANG (salah — menyebabkan SyntaxError, seluruh proyektor crash):
'onclick=\"showScreen(\'\" + t.id + \"\')\">\'

// FIX — gunakan &quot; untuk quote di dalam HTML attribute:
'onclick=\"showScreen(&quot;\' + t.id + \'&quot;)\">\''
```
**Efek bug:** Proyektor blank total, peserta tidak muncul, semua screen tidak jalan.

---

#### BUG-3: Duplikat pertanyaan Game 3 saat Save
**File:** `admin.html`
**Lokasi:** fungsi `saveG3Questions()`
**Problem:** `sbUpsert` tanpa `id` eksplisit → setiap save buat row baru, tidak update.
```javascript
// FIX: Delete dulu semua via REST filter, lalu insert ulang dengan id eksplisit
await fetch(SUPABASE_URL + '/rest/v1/ras_g3_questions?urutan=gte.0', {
  method: 'DELETE', headers: sbHeaders()
});
for (let i = 0; i < g3Questions.length; i++) {
  await sbUpsert('ras_g3_questions', { id: i + 1, urutan: i, pertanyaan: g3Questions[i] });
}
```

---

#### BUG-4: Foto tidak bisa dipilih di tab game (Timeline, Memory Match, dll)
**File:** `admin.html`
**Lokasi:** fungsi `g1RenderPhotoAssign()` dan `togglePhotoAssign()`
**Root cause:** BUG-1 di atas — karena `game_tags` undefined, filter selalu kosong.
**Fix:** Selesaikan BUG-1 terlebih dahulu.

---

#### BUG-5: Tidak ada fungsi Delete foto komedian
**File:** `admin.html`
**Lokasi:** fungsi `renderKomedianGrid()`
**Problem:** Kartu komedian hanya punya tombol upload, tidak ada tombol hapus foto.
```javascript
// Tambah tombol delete di setiap komedian-card yang sudah ada foto:
'<button onclick="deleteKomedianPhoto(' + k.id + ')" ...>🗑️</button>'

// Tambah fungsi:
async function deleteKomedianPhoto(id) {
  komedianPhotos[id] = '';
  await sbUpsert('ras_komedian_photos', { komedian_id: id, photo_url: '', updated_at: new Date().toISOString() });
  renderKomedianGrid();
  showNotif('🗑️ Foto komedian dihapus.');
}
```

---

### 🟡 FITUR BARU — PROYEKTOR

#### FEAT-1: Auto-switch proyektor mengikuti game yang dibuka admin
**File:** `proyektor.html`
**Logic:**
- Subscribe realtime ke `ras_open_games`
- Saat admin buka game baru → proyektor otomatis pindah ke screen game tersebut
- Kalau admin tutup semua game → proyektor kembali ke `screenLobby`
- Urutan prioritas: Game yang paling baru dibuka = yang ditampilkan
```javascript
// Mapping game_id → screen_id
const GAME_SCREEN_MAP = {
  1: 'screenGame1',
  2: 'screenGame2',  // baru
  3: 'screenGame3',
  4: 'screenGame4',
  5: 'screenGame5',
  6: 'screenGame6',
};

function autoSwitchScreen(openGameIds) {
  if (!openGameIds || openGameIds.length === 0) {
    showScreen('screenLobby');
    return;
  }
  const lastOpened = openGameIds[openGameIds.length - 1];
  const targetScreen = GAME_SCREEN_MAP[lastOpened];
  if (targetScreen) showScreen(targetScreen);
}
```

---

#### FEAT-2: Screen Game 2 di proyektor — Foto blur → jelas
**File:** `proyektor.html`
**Tambah:** `<div id="screenGame2" class="screen">` baru
**Konten:**
- Foto besar di tengah, animasi CSS blur dari 20px → 0px sesuai timer
- Counter "X peserta sudah menjawab / Y total"
- Nama foto (disembunyikan sampai waktu habis)
- Poin yang tersisa (countdown visual)
- Subscribe ke `ras_g2_state` realtime

**CSS animasi blur:**
```css
.foto-reveal {
  filter: blur(20px);
  transition: filter linear;
}
/* JS kontrol duration sesuai timer */
```

---

#### FEAT-3: Screen Game 4 di proyektor — 5 Foto per Ronde
**File:** `proyektor.html`
**Upgrade:** `screenGame4` yang ada sekarang hanya leaderboard → tambah tampilan foto
**Layout:**
- Bagian atas: 5 foto anggota keluarga tampil acak (yang harus diurutkan)
- Tengah: timer ronde countdown
- Bawah: leaderboard skor setelah ronde selesai
- Subscribe ke `ras_g4_state` realtime

---

#### FEAT-4: Tab Result / Pemenang di proyektor
**File:** `proyektor.html`
**Tambah:** `screenResult` baru
**Konten:**
- Trigger: admin klik tombol "Tampilkan Hasil Akhir" di admin panel
- Kompilasi otomatis skor dari semua tabel: `ras_g1_scores`, `ras_g4_scores`, `ras_g5_results`, dll
- Tampilkan podium 🥇🥈🥉 dengan nama + total poin + breakdown per game
- Animasi reveal dramatis (fade in satu per satu dari juara 3 → juara 1)

**Struktur tabel rekap:**
```
Nama Peserta | G1 | G2 | G3 | G4 | G5 | G6 | Total
Budi         | 80 | 350| 200| 90 | —  | 400| 1120  ← 🥇
Sari         | 60 | 400| 150| 70 | —  | 300| 980   ← 🥈
```

---

### 🟡 FITUR BARU — GAMEPLAY

#### FEAT-5: Game 2 — Ubah menjadi sinkron
**File:** `rasmita-family-gathering-2026.html`
**Problem saat ini:** Setiap peserta main sendiri-sendiri, foto berbeda per orang.
**Target:** Semua peserta lihat foto yang sama, dikontrol admin.

**Flow baru:**
1. Admin pilih foto untuk Game 2 di admin panel, klik "Mulai"
2. State tersimpan di `ras_g2_state`: `{ current_photo_id, timer, status, answers_count }`
3. Semua HP peserta subscribe → tampilkan foto yang sama
4. Timer countdown berjalan sinkron
5. Peserta jawab di HP → `answers_count` bertambah di proyektor
6. Admin klik "Next" atau otomatis setelah timer habis → foto berikutnya

**Tabel baru `ras_g2_state`:**
```sql
CREATE TABLE ras_g2_state (
  id              INT PRIMARY KEY DEFAULT 1,
  status          TEXT DEFAULT 'waiting',  -- waiting | playing | reveal | done
  current_idx     INT DEFAULT 0,           -- index foto sekarang
  photos_json     TEXT DEFAULT '[]',       -- array foto yang dipakai
  timer_seconds   INT DEFAULT 30,          -- sisa waktu
  answers_json    TEXT DEFAULT '{}',       -- { player_name: answer }
  scores_json     TEXT DEFAULT '{}',       -- { player_name: total_score }
  session_id      TEXT DEFAULT '',
  updated_at      TIMESTAMPTZ DEFAULT NOW()
);
```

---

#### FEAT-6: Game 4 — Admin kontrol per ronde + timer
**File:** `rasmita-family-gathering-2026.html` + `admin.html`
**Target:** Admin klik "Mulai Ronde X" atau otomatis dengan timer antar ronde.

**Flow baru:**
1. Admin setup: pilih jumlah ronde (3–5), pilih foto di admin panel
2. Klik "Mulai Ronde 1" → 5 foto acak tampil di proyektor + HP peserta
3. Timer 60 detik berjalan (atau dikustomisasi admin)
4. Peserta drag & drop di HP
5. Timer habis → semua submit otomatis → skor dihitung
6. Proyektor tampilkan jawaban benar + delta skor
7. Admin klik "Ronde Berikutnya" (atau otomatis setelah 10 detik)

**Tabel baru `ras_g4_state`:**
```sql
CREATE TABLE ras_g4_state (
  id              INT PRIMARY KEY DEFAULT 1,
  status          TEXT DEFAULT 'waiting',  -- waiting | playing | reveal | done
  current_ronde   INT DEFAULT 0,
  total_ronde     INT DEFAULT 3,
  photos_json     TEXT DEFAULT '[]',       -- foto 5 per ronde [[r1], [r2], [r3]]
  timer_seconds   INT DEFAULT 60,
  answers_json    TEXT DEFAULT '{}',       -- { player_name: [order_array] }
  scores_json     TEXT DEFAULT '{}',       -- { player_name: total_score }
  session_id      TEXT DEFAULT '',
  updated_at      TIMESTAMPTZ DEFAULT NOW()
);
```

---

#### FEAT-7: Game 5 — Komedian sesuai gender peserta
**File:** `rasmita-family-gathering-2026.html`
**Problem saat ini:** Komedian pria dan wanita campur, tidak ada filter gender.
**Target:** 
- Saat peserta daftar → pilih gender (Pria / Wanita)
- Game 5 → hasil komedian disesuaikan gender
- Komedian pria → match ke list komedian pria
- Komedian wanita → match ke list komedian wanita

**Perubahan form registrasi:**
```html
<!-- Tambah di form join -->
<div class="form-group">
  <label>Jenis Kelamin</label>
  <div class="gender-pick">
    <button onclick="setGender('pria')">👨 Pria</button>
    <button onclick="setGender('wanita')">👩 Wanita</button>
  </div>
</div>
```

**Simpan ke ras_players:**
```javascript
await sbUpsert('ras_players', {
  name, photo_url, photo_b64,
  gender: state.gender || 'pria',  // ← tambah kolom gender
  joined_at: new Date().toISOString()
});
```

---

### 🟡 DATABASE — SQL Patches

#### SQL-1: Tambah tabel `ras_g2_state`
#### SQL-2: Tambah tabel `ras_g4_state` (replace `ras_g4_scores` yang lama)
#### SQL-3: Tambah kolom `gender` ke `ras_players`
#### SQL-4: Tambah tabel `ras_results_summary` untuk rekap akhir
#### SQL-5: Aktifkan Realtime untuk semua tabel baru

---

### 🟡 DATABASE KOMEDIAN — Penggantian

**Komedian yang perlu diganti** (yang tidak ada foto / slot kosong):
Berdasarkan screenshot, yang "Klik upload" (belum ada foto):
- ID 60 Kiky Saputra (pria)
- ID 67 Oken
- ID 70 Pak Besut
- ID 72 Cagur
- ID 74 Ical Residivis
- ID 75 Al Kausar
- ID 118 Nora Alexandra (wanita)
- ID 121 Shinta Bachir
- ID 124 Nita Thalia
- ID 125 Rohimah

**Kandidat pengganti komedian wanita terkenal:**
| ID | Diganti dengan | Alasan |
|----|---------------|--------|
| 118 | **Yati Surachman** | Legenda srimulat |
| 121 | **Nurul Arifin** | Terkenal luas |
| 124 | **Nining Meida** | Komedian Sunda terkenal |
| 125 | **Mpok Atiek** | Betawi, sangat populer |

**Kandidat pengganti komedian pria terkenal:**
| ID | Diganti dengan | Alasan |
|----|---------------|--------|
| 60 | **Cak Lontong** | Sangat terkenal, stand-up |
| 67 | **Boris Bokir** | Stand-up populer |
| 70 | **Abdur Arsyad** | Stand-up NTT, populer |
| 72 | **Pepen** | Komedian senior |
| 74 | **Tessy** | Legenda srimulat |
| 75 | **Ucok Baba** | Komedian terkenal |

---

## 🏗️ Urutan Pengerjaan (Recommended)

```
SPRINT 1 — Bug Fixes (semua harus selesai dulu):
  ├── BUG-1: Fix game_tags load di admin.html
  ├── BUG-2: Fix SyntaxError proyektor.html
  ├── BUG-3: Fix duplikat pertanyaan G3
  ├── BUG-4: Fix foto tidak bisa dipilih (solved by BUG-1)
  └── BUG-5: Tambah delete foto komedian

SPRINT 2 — Database & SQL:
  ├── SQL-1: Tabel ras_g2_state
  ├── SQL-2: Tabel ras_g4_state
  ├── SQL-3: Kolom gender di ras_players
  ├── SQL-4: Tabel ras_results_summary
  └── SQL-5: Aktifkan Realtime semua tabel baru

SPRINT 3 — Game 2 Sinkron: ✅ SELESAI
  ├── ✅ FEAT-5: Game 2 gameplay sinkron (game HTML)
  ├── ✅ FEAT-2: Screen Game 2 di proyektor (blur→jelas + live answers)
  ├── ✅ FEAT-1: Auto-switch proyektor
  ├── ✅ FEAT-4: Screen Result + renderResult()
  └── ✅ Admin panel: kontrol Game 2 (pilih foto, mulai, reveal, next, reset)

SPRINT 4 — Game 4 Upgrade:
  ├── FEAT-6: Game 4 per ronde + timer (game HTML)
  ├── FEAT-3: Screen Game 4 di proyektor (5 foto per ronde)
  └── Admin panel: kontrol Game 4 (setup ronde, mulai)

SPRINT 5 — Proyektor Auto-switch + Result:
  ├── FEAT-1: Auto-switch proyektor
  └── FEAT-4: Screen Result / Pemenang

SPRINT 6 — Game 5 Gender + Komedian:
  ├── FEAT-7: Filter komedian by gender
  └── Update database komedian (ganti yang kosong)
```

---

## 📽️ Proyektor — Screen List (v4.0)

| Screen ID | Trigger | Konten |
|-----------|---------|--------|
| `screenWelcome` | Default saat buka | Nama event, URL, QR code, daftar game |
| `screenLobby` | Admin tutup semua game | Daftar peserta live |
| `screenGame1` | Admin buka Game 1 | Grid kartu Memory Match + leaderboard tim |
| `screenGame2` | **[BARU]** Admin buka Game 2 | Foto blur→jelas + counter jawaban |
| `screenGame3` | Admin buka Game 3 | Hasil voting real-time per pertanyaan |
| `screenGame4` | Admin buka Game 4 | **[UPGRADE]** 5 foto per ronde + timer + leaderboard |
| `screenGame5` | Admin buka Game 5 | Grid hasil komedian semua peserta |
| `screenGame6` | Admin buka Game 6 | Lirik sing-along besar |
| `screenResult` | **[BARU]** Admin klik "Tampilkan Hasil" | Podium pemenang + rekap skor semua game |

**Auto-switch logic:**
```
ras_open_games berubah → proyektor cek game_ids terakhir
  → game_ids kosong          → screenLobby
  → game_ids = [1]           → screenGame1
  → game_ids = [1,2]         → screenGame2 (game terbaru)
  → game_ids = [1,2,3]       → screenGame3 (game terbaru)
  → semua game tutup         → screenLobby
```

---

## 🎭 Database Komedian (100 Komedian)

**Pria (ID 1–75, total ~50 aktif):**
Sule, Dono, Kasino, Indro Warkop, Benyamin Sueb, Bing Slamet, Ateng, Iskak, Tarzan, Jojon, Basuki, Kadir, Miing Bagito, Unang Bagito, Didin Bagito, Doyok, Polo, Cahyono, Tukul Arwana, Olga Syahputra, Parto Patrio, Aming, Aziz Gagap, Komeng, Andre Taulany, Wendy Cagur, Narji, Ruben Onsu, Desta, Vincent Rompies, Raditya Dika, Ernest Prakasa, Pandji Pragiwaksono, Soleh Solihun, Coki Pardede, Mongol Stres, Kemal Palevi, Arie Kriting, Ryan Adriandhy, Bintang Emon, Ge Pamungkas, Arief Muhammad, Akbar Kobar, Nopek Novian, Mamat Alkatiri, Dzawin Nur, Fajar Nugra, Babe Cabita, Yudha Keling, Ridwan Remin, Rigen Rakelna, Sammy Notaslimboy, Wendy Walters, Alex Cendana, Deddy Corbuzier, Uus, Dodit Mulyanto, Muhadkly Acho, Praz Teguh + pengganti

**Wanita (ID 101–125, total 25):**
Nunung, Kiky Saputri, Soimah, Rina Nose, Arafah Rianti, Asri Welas, Tika Panggabean, Fitri Tropica, Melaney Ricardo, Sarah Sechan, Mpok Nori, Hesti Purwadinata, Nycta Gina, Ayu Dewi, Ria Ricis, Cinta Laura, Inul Daratista, ~~Nora Alexandra~~→**Yati Surachman**, Yuki Kato, Uut Permatasari, ~~Shinta Bachir~~→**Nurul Arifin**, Titi DJ, Ussy Sulistiawaty, ~~Nita Thalia~~→**Nining Meida**, ~~Rohimah~~→**Mpok Atiek**

---

## ✅ Checklist Persiapan Event

### H-7
- [ ] Jalankan `SUPABASE-SETUP-ALL-IN-ONE.sql` di Supabase SQL Editor
- [ ] Jalankan `PATCH-v4.0.sql` (patch tabel baru)
- [ ] Test buka ketiga file HTML di Chrome/Edge
- [ ] Kumpulkan foto anggota keluarga (min 15, ideal 25)

### H-3
- [ ] Upload semua foto keluarga di Admin → Foto Keluarga
- [ ] Assign foto ke tiap game di tab Memory Match / Tebak Siapa / Siapa Paling / Timeline
- [ ] Set nama event, tanggal, lokasi, kode room
- [ ] Upload foto komedian yang tersedia
- [ ] Upload ke Netlify, test URL
- [ ] Siapkan MP3 lagu (opsional, ada melodi sintetis sebagai fallback)

### H-1
- [ ] Test seluruh 6 game end-to-end
- [ ] Test proyektor auto-switch
- [ ] Test screen Result/Pemenang
- [ ] Cek kabel HDMI + speaker eksternal
- [ ] Backup folder ke flashdisk

### Hari H — 30 Menit Sebelum
- [ ] Buka proyektor.html → F11 fullscreen di layar besar
- [ ] Buka admin.html di laptop/HP admin
- [ ] Pastikan peserta bisa scan QR / buka URL
- [ ] **Jangan buka semua game sekaligus** — buka satu per satu sesuai giliran

---

## 🆘 Troubleshooting

| Masalah | Solusi |
|---------|--------|
| Proyektor blank / tidak jalan | Cek SyntaxError di console → update ke v4.0 |
| Foto tidak bisa dipilih di tab game | Pastikan SQL PATCH-v4.0 sudah dijalankan (kolom game_tags) |
| Peserta tidak muncul di proyektor | Cek ras_players di Supabase Table Editor — ada data? |
| Duplikat pertanyaan Game 3 | Update ke v4.0 (bug fix) |
| Proyektor tidak auto-switch | Pastikan Realtime aktif untuk ras_open_games |
| Game 2 tidak sinkron | Pastikan ras_g2_state sudah dibuat dan Realtime aktif |
| Game 4 tidak tampil foto | Pastikan ras_g4_state sudah dibuat |
| Komedian tidak sesuai gender | Pastikan peserta pilih gender saat registrasi |
| Upload foto gagal | Cek bucket Storage bersifat Public + policy INSERT ada |
| Audio tidak bunyi | Tap layar dulu (kebijakan autoplay browser) |

---

## 📋 Changelog

### v4.0-sprint3 (Sprint 3 Done ✅)
- ✅ **Sprint 3:** Game 2 sinkron via ras_g2_state
- ✅ **Sprint 3:** Screen Game 2 proyektor (blur→jelas + live answers)
- ✅ **Sprint 3:** Admin panel Game 2 lengkap (pilih foto, mulai, reveal, next, reset)
- ✅ **Sprint 3:** FEAT-1 Auto-switch proyektor mengikuti game yang dibuka
- ✅ **Sprint 3:** screenResult + renderResult() (podium + skor table)
- ✅ **Sprint 3:** ras_g2_state + ras_results_summary subscribe Realtime

### v4.0-dev (Target)
- 🔧 **Fix:** game_tags tidak ter-load dari Supabase ke FAMILY_MEMBERS
- 🔧 **Fix:** SyntaxError proyektor.html line 695 (buildNavTabs quotes rusak)
- 🔧 **Fix:** Duplikat pertanyaan Game 3 saat save
- 🔧 **Fix:** Tambah delete foto komedian
- ✨ **Baru:** Game 2 sinkron — semua peserta tebak foto yang sama bersamaan
- ✨ **Baru:** Screen Game 2 di proyektor (foto blur → jelas + counter jawaban)
- ✨ **Baru:** Screen Game 4 upgrade (5 foto per ronde tampil di proyektor)
- ✨ **Baru:** Game 4 per ronde + timer (admin klik mulai atau auto timer)
- ✨ **Baru:** Proyektor auto-switch mengikuti game yang dibuka admin
- ✨ **Baru:** Screen Result/Pemenang — podium + rekap skor semua game
- ✨ **Baru:** Game 5 filter komedian sesuai gender peserta
- ✨ **Baru:** Ganti komedian yang tidak ada foto dengan nama lebih terkenal
- ✨ **Baru:** Kolom gender di form registrasi peserta
- ✨ **Baru:** Tabel ras_g2_state, ras_g4_state, ras_results_summary

### v3.2
- Fix Supabase anon key format
- Fix SyntaxError lirik sing-along
- Fix quote escaping admin.html
- Fix credentials proyektor.html

### v3.0
- Versi awal: 6 game, integrasi Supabase, mode proyektor

---

## ℹ️ Info Event

**Tanggal:** 28 Maret 2026
**Lokasi:** Wisma Mandiri, Bandung, Jawa Barat
**Kode Room Default:** FAM2026
**URL Game:** rasmita-gathering.netlify.app
**Short URL:** bit.ly/rasmitas
**Supabase Project:** ykibpxiovrslknagmnpq.supabase.co

---

*Dibuat dengan ❤️ untuk Reuni Keluarga Rasmita 2026 — Bandung · Wisma Mandiri*
