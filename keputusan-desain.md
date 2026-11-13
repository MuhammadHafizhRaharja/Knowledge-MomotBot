---
tags: [momot, keputusan]
---

# Keputusan desain

Kembali ke [peta](README.md). Format tiap keputusan: apa yang dipilih, lalu
alasannya.

## Perangkat keras

- **XIAO ESP32S3 menggantikan modul WROOM di PCB sendiri.** Upload dan Serial
  langsung lewat USB-C, ada PSRAM 8 MB, dan rakitan cukup dengan kabel serta
  papan lubang.
- **Mic dan amp berbagi satu port I2S dupleks.** XIAO hanya punya 11 pin, dan
  format keduanya sudah sama.
- **Baterai lewat pad BAT, amplifier dari VBAT.** Lihat
  [Perangkat keras](perangkat-keras.md#daya).

## Firmware

- **Satu sumber di `src/` untuk Arduino dan PlatformIO.** File `.ino` kosong
  membuat Arduino mengompilasi `src/`, sementara PlatformIO mengabaikan `.ino`
  di luar `src/`.
- **Blok pin dipilih dengan makro board**, sehingga satu kode bisa
  dikompilasi untuk XIAO maupun WROOM.
- **Audio diunggah sebagai mu-law 8 kHz.** Ukurannya setengah PCM16.
  Opus lebih kecil, tapi pustaka Arduino-nya berlisensi GPL-3.0.
- **Bahasa STT dipaksa, tidak ditebak.** Pada rekaman 1–2 detik, deteksi
  otomatis sering meleset ke bahasa Inggris.
- **NVS hanya ditulis bila `rev` setelan naik**, supaya flash tidak aus.

## Cloud

- **`hello` menggabungkan laporan dan setelan**, jadi hanya satu handshake TLS.
- **OTA per papan** (`momot-<papan>-<versi>.bin`), supaya biner WROOM tidak
  pernah sampai ke XIAO. Lihat [OTA](ota.md).
- **Waktu disimpan sebagai BIGINT milidetik.** DATETIME tidak membawa zona,
  dan TIMESTAMP berubah diam-diam mengikuti zona sesi.
- **utf8mb4**, supaya nama perangkat dengan emoji tersimpan utuh.
- **scrypt, bukan bcrypt/argon2.** Tidak perlu kompilasi native di Alpine.

## Aplikasi

- **Tanpa kerangka kerja UI, dibungkus Capacitor.** Android dan iOS dari satu
  sumber, dan bisa diuji di Chrome.
- **Kontrak di satu berkas (`kontrak.js`).**
- **Daftarkan ke cloud dulu, baru kirim lewat BLE.** Token ikut dalam tulisan
  yang sama dengan WiFi.
- **Tidak ada angka karangan.** Meter emosi disembunyikan selama cloud belum
  mengirim `happy`.

## Nama

- **Nori menjadi Momot** di ketiga repo, termasuk nama BLE (`Momot-XXXX`),
  batas multipart, dan variabel lingkungan (`MOMOT_DATA`, `MOMOT_VERSI`).
  Unit lama yang masih beriklan sebagai `Nori-XXXX` perlu di-flash ulang.
