# Dokumen Desain Game: Lightbound (MVP)

Dokumen ini menjelaskan cakupan Minimum Viable Product (MVP) untuk game multiplayer kooperatif "Lightbound".

---

### 1. Ringkasan Game

- **Nama Game:** Lightbound
- **Konsep Inti:** Sebuah game kooperatif untuk 3 pemain di mana sebuah tim harus melindungi satu pemain kunci, "Pembawa Obor," saat mereka menavigasi labirin gelap yang dibuat secara acak dan penuh dengan monster.
- **Lingkaran Permainan (Core Loop):** Masuk lobi -> Peran diberikan acak -> Labirin dibuat -> Tim menavigasi & bertarung -> Mencapai pintu keluar (Menang) atau Pembawa Obor tumbang (Kalah) -> Kembali ke lobi.
- **Platform:** Web (PC/Desktop)
- **Teknologi:** React Three Fiber (R3F) & Playroom.js

### 2. Mekanik Inti (Fokus MVP)

#### a. Sesi Multiplayer & Lobi

- **Alur:** Pemain membuka link game. Game akan dimulai secara otomatis saat 3 pemain telah terhubung.
- **Tugas Playroom:** Mengelola state pemain yang terhubung dan memulai sesi permainan.

#### b. Generasi Labirin Acak

- **Struktur:** Labirin berbasis grid (misalnya, 20x20 sel).
- **Algoritma:** Menggunakan algoritma sederhana seperti _Recursive Backtracking_ untuk menghasilkan jalur.
- **Fitur:** Satu titik "Mulai" dan satu titik "Keluar" yang ditempatkan secara acak dan berjauhan.
- **Tugas Playroom:** State labirin (seed atau layout) harus disinkronkan ke semua pemain agar medannya identik.

#### c. Peran Pemain (Disederhanakan & Acak)

Untuk MVP, kita menggunakan 3 peran yang diberikan secara acak saat game dimulai:

1.  **Pembawa Obor (The Torchbearer)**

    - **Tugas:** Menjadi sumber cahaya dan tujuan misi.
    - **Atribut:** Kecepatan gerak lambat (misalnya, 50% dari pemain lain). Tidak bisa menyerang.
    - **Kondisi Kalah:** Jika kesehatan pemain ini habis, tim kalah.

2.  **Pelindung (The Guardian)**

    - **Tugas:** Melindungi Pembawa Obor dari serangan jarak dekat.
    - **Atribut:** Kecepatan gerak normal.
    - **Aksi:** Serangan `Tebasan` jarak dekat dengan cooldown singkat.

3.  **Penyerang (The Striker)**
    - **Tugas:** Menghabisi musuh dari jarak aman.
    - **Atribut:** Kecepatan gerak normal.
    - **Aksi:** Serangan `Tembakan` proyektil jarak jauh dengan cooldown singkat.

#### d. Mekanik Cahaya & Kegelapan

- **Implementasi R3F:** Gunakan satu `PointLight` yang terikat pada posisi model Pembawa Obor.
- **Aturan:** Area di luar radius cahaya akan benar-benar gelap. Semua pemain dan musuh hanya bisa dilihat jika berada di dalam area terang. Ini memaksa tim untuk selalu berdekatan.

#### e. Sistem Musuh Sederhana

- **Jenis Musuh:** Cukup satu jenis, sebut saja "Bayangan" (Shade).
- **Perilaku (AI):** Muncul secara berkala dari area gelap. Jika pemain masuk dalam jangkauan deteksi, musuh akan bergerak lurus ke arah Pembawa Obor.
- **Tugas Playroom:** Sinkronisasi posisi, kesehatan, dan status setiap musuh.

### 3. Kontrol & Aset Visual (Sederhana)

- **Kontrol Pemain:**
  - `WASD` atau `Tombol Panah`: Bergerak.
  - `Klik Kiri Mouse`: Menggunakan aksi/serangan.
- **Aset Visual (Placeholder):**
  - **Pemain:** Gunakan bentuk geometris sederhana.
    - Pembawa Obor: Bola (Sphere) berwarna kuning.
    - Pelindung: Kubus (Box) berwarna biru.
    - Penyerang: Kerucut (Cone) berwarna merah.
  - **Musuh:** Bidang datar (Plane) berwarna hitam atau sistem partikel sederhana.
  - **Lingkungan:** Dinding labirin dibuat dari kubus abu-abu.

### 4. Kondisi Menang & Kalah

- **Kondisi Menang:** Objek pemain Pembawa Obor berhasil menyentuh trigger di titik "Keluar".
- **Kondisi Kalah:** Kesehatan Pembawa Obor mencapai 0.
- **UI Akhir:** Tampilkan teks sederhana di tengah layar: "MISI BERHASIL" atau "MISI GAGAL".

### 5. Spesifikasi Teknis & Alur

#### a. Tumpukan Teknologi (Tech Stack)

- **Frontend & Rendering:** React, React Three Fiber (R3F)
- **Manajemen State 3D:** Zustand (untuk state lokal di client)
- **Networking & State Multiplayer:** Playroom.js
- **Styling:** Tailwind CSS

#### b. Alur Data Multiplayer

1.  **Player Join:** Pemain baru terhubung, Playroom menetapkan state awal (posisi, peran acak).
2.  **Input Pemain:** Aksi pemain (gerak, serang) dikirim sebagai event ke Playroom.
3.  **Sinkronisasi State:** Playroom menyiarkan perubahan state (posisi baru, musuh baru, perubahan HP) ke semua pemain.
4.  **Render di Client:** Setiap client menerima state baru dan R3F merender ulang tampilan sesuai data tersebut.
