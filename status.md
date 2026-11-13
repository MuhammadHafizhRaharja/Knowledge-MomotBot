---
tags: [momot, status]
---

# Status dan pekerjaan terbuka

Kembali ke [peta](README.md). Keadaan per 2026-11-13.

## Selesai

- Firmware dipindah ke XIAO ESP32S3: pin, I2S dupleks, PlatformIO env,
  dan dokumen wiring.
- OTA per papan di firmware dan cloud.
- Nama Nori dihapus dari ketiga repo.
- Audit kontrak firmware, cloud, dan aplikasi: BLE, rute perangkat, dan rute
  aplikasi cocok satu sama lain.
- Aplikasi tidak lagi menampilkan nilai emosi karangan.
- Ketiga repo digabung ke `main`.

## Terverifikasi

| Bagian | Cara | Hasil |
|---|---|---|
| Firmware XIAO | `arduino-cli`, core esp32 3.3.12 | Lolos, 74% |
| Firmware WROOM | `arduino-cli` | Lolos, 80% |
| Sketch tes | MomotTes, TesSpeaker, TesWajah | Lolos |
| Aplikasi | `npm run build`; Playwright dengan fixture | Lolos |
| Cloud | `node --check`; logika OTA diuji terpisah | Lolos |

## Belum

- [ ] Deploy cloud ke produksi. Pastikan server memakai `MOMOT_DATA` dan
      `MOMOT_VERSI`.
- [ ] Uji integrasi cloud (`uji/firmware.mjs`) terhadap MySQL lokal.
- [ ] Uji fisik di XIAO: pendaftaran BLE, mic dan speaker, baterai, OTA.
- [ ] Firmware melaporkan emosi (`happy`) ke cloud agar meter emosi di
      aplikasi berisi data.
- [ ] Jadwal: tersimpan dan terkirim, tapi belum dijalankan firmware.
- [ ] Pekerjaan keamanan sebelum dijual: lihat [Keamanan](keamanan.md).
