---
tags: [momot, keamanan, privasi]
---

# Keamanan dan privasi

Kembali ke [peta](README.md).

## Yang sudah

- **Kredensial penyedia AI hanya di cloud.** Perangkat memegang token
  miliknya sendiri. Menghapus perangkat dari aplikasi langsung mencabut
  aksesnya.
- **Token sesi dan token perangkat disimpan sebagai hash.** Kalau basis data
  bocor, isinya tidak bisa langsung dipakai untuk masuk.
- **Sandi akun di-hash dengan scrypt bergaram** (bawaan Node, tanpa modul
  native).
- **Sandi WiFi tidak pernah lewat cloud.** Sandi berjalan dari HP lewat BLE
  dan berhenti di NVS perangkat.
- **404, bukan 403**, untuk perangkat yang tidak boleh diakses.
- **Pembatas laju** untuk daftar, masuk (per IP dan per email), dan
  perangkat.
- **Ekspor dan hapus akun** tersedia (hak portabilitas, UU PDP).
- **Izin BLE Android tanpa lokasi** (`neverForLocation`).

## Yang belum, dan harus ada sebelum dijual

- **Lupa sandi dan verifikasi email.** Butuh pengiriman email.
- **Pasangan BLE tanpa verifikasi.** Siapa pun dalam jangkauan saat mode
  pendaftaran bisa mendaftarkan Momot ke akunnya. Perlu kode di layar Momot
  yang diketik di aplikasi.
- **Sertifikat TLS belum diperiksa di firmware** (`setInsecure()`).
- **Pemberitahuan "ini AI" dan rujukan krisis.** Wajib menurut aturan seperti
  California SB 243 dan New York bila melayani pengguna di sana.
- **Pembatas laju di memori.** Perlu Redis kalau cloud berjalan di beberapa
  salinan.
