---
tags: [momot, ota, firmware, cloud]
---

# OTA firmware per papan

Kembali ke [peta](README.md).

Pin XIAO dan WROOM berbeda, jadi biner satu papan tidak boleh terpasang di
papan lain. Karena itu firmware melapor kode papan, dan cloud memilih berkas
sesuai kode tersebut.

| `papan` | Perangkat | Berkas di `<MOMOT_DATA>/firmware/` |
|---|---|---|
| `xiao-s3` | Seeed XIAO ESP32S3 (sekarang) | `momot-xiao-s3-<versi>.bin` |
| `s3` | ESP32-S3-WROOM-1 N16R8 (lama) | `momot-s3-<versi>.bin` |

Unit C3 lama tidak mengirim `papan`, jadi tidak pernah ditawari (partisinya
tidak punya slot OTA).

## Alur

1. Firmware mengirim `hello` berisi `firmware` (`FW_VERSION`) dan `papan`.
2. Bila ada berkas untuk papan itu dan versinya **berbeda**, cloud membalas
   `firmware: {versi, md5, ukuran}`.
3. Saat diam, perangkat mengunduh `GET /v1/device/firmware?papan=<papan>`,
   memeriksa md5, menulis ke slot OTA, lalu restart.
4. Firmware baru harus berhasil `hello` sebelum dianggap sehat. Kalau gagal,
   perangkat kembali ke versi lama (rollback).

## Merilis versi

1. Naikkan `FW_VERSION` di `config.h`.
2. Compile, lalu salin `ESP_VoCat_Main.ino.bin` ke
   `<MOMOT_DATA>/firmware/momot-xiao-s3-<versi>.bin`.
3. Setel `FIRMWARE_VERSI=<versi>` di cloud, lalu restart.

Versi dibandingkan sama/beda, bukan besar/kecil, jadi menurunkan versi cukup
dengan mengganti angkanya. Kosongkan `FIRMWARE_VERSI` untuk mematikan OTA.
Hasil md5 di-cache per papan dan dihitung ulang bila berkasnya berubah.
