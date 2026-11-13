---
tags: [momot, peta]
---

# Knowledge MomotBot

Basis pengetahuan **Momot**, robot pendamping AI di meja: layar wajah bulat,
mikrofon, speaker, dan WiFi. Catatan ini merangkum tiga repo yang membentuk
sistemnya. Ditulis untuk dibaca di Obsidian, dan tetap terbaca di GitHub.

| Repo | Peran | Catatan |
|---|---|---|
| ESP_VoCat | Firmware perangkat (Seeed XIAO ESP32S3) | [Firmware](firmware.md) |
| CLOUD-COMPANION | Backend: akun, perangkat, penerus AI, OTA | [Cloud](cloud.md) |
| Ai-Companions-apis | Aplikasi HP (Capacitor) | [Aplikasi](aplikasi.md) |

## Mulai dari sini

1. [Arsitektur dan alur data](arsitektur.md): siapa bicara ke siapa.
2. [Perangkat keras XIAO ESP32S3](perangkat-keras.md): pin, daya, wiring.
3. Kontrak antar-repo:
   - [Kontrak BLE](kontrak-ble.md): aplikasi ke perangkat, sekali saat mendaftar.
   - [Kontrak API perangkat](kontrak-api-perangkat.md): firmware ke cloud.
   - [Kontrak API aplikasi](kontrak-api-aplikasi.md): aplikasi ke cloud.
4. [OTA firmware](ota.md): pembaruan per papan.
5. [Keamanan dan privasi](keamanan.md).
6. [Keputusan desain](keputusan-desain.md): alasan di balik pilihan penting.
7. [Status dan pekerjaan terbuka](status.md).

## Ringkas dalam tiga kalimat

Aplikasi mendaftarkan Momot ke cloud, lalu mengirim WiFi dan token perangkat
lewat Bluetooth. Sesudah itu Momot hanya bicara ke cloud: melapor, mengambil
setelan, dan meneruskan suara ke penyedia AI. Kredensial penyedia AI tidak
pernah ada di perangkat.

---
Diperbarui 2026-11-13, sesudah migrasi ke XIAO ESP32S3 dan penggantian nama
Nori menjadi Momot.
