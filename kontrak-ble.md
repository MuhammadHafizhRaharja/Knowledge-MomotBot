---
tags: [momot, kontrak, ble]
---

# Kontrak BLE (aplikasi ke perangkat)

Kembali ke [peta](README.md). Sumber: `prov_ble.cpp` di firmware dan
`src/kontrak.js` di aplikasi. Keduanya harus diubah bersamaan.

Momot mengiklankan diri sebagai **`Momot-XXXX`** (dua byte terakhir MAC).
Penyaringan sebenarnya memakai UUID layanan. Firmware meminta MTU 247;
aplikasi menulis dalam potongan maksimal 180 byte, dan firmware menumpuk
potongan sampai JSON-nya sah (maksimal 1024 byte).

| Nama | UUID | Arah | Isi |
|---|---|---|---|
| SVC | `9a1e0000-7b5c-4f1e-9d2a-3c6b8e4f5a70` | layanan | |
| INFO | `9a1e0001-7b5c-4f1e-9d2a-3c6b8e4f5a70` | baca | `{nama, fw, ssid, kode, wifi, ip, rssi}` |
| NETS | `9a1e0002-7b5c-4f1e-9d2a-3c6b8e4f5a70` | baca | `[{ssid, rssi, lock}]` |
| PROV | `9a1e0003-7b5c-4f1e-9d2a-3c6b8e4f5a70` | tulis | `{ssid, pass, cloud, deviceId, deviceToken}` atau `{cmd: "scan"}` |
| STAT | `9a1e0004-7b5c-4f1e-9d2a-3c6b8e4f5a70` | notify | `{state, msg?}` |

## Nilai `state`

| State | Arti |
|---|---|
| `ready` | Siap menerima kredensial |
| `scanning` | Memindai jaringan WiFi (sesudah `{cmd: "scan"}`) |
| `saved` | Tersimpan; perangkat akan restart, dan BLE putus = berhasil |
| `error` | Ditolak; `msg` berisi alasannya (mis. `ssid`, `cloud`, atau `deviceToken` kosong) |

`ssid`, `cloud`, dan `deviceToken` wajib. Garis miring di ujung `cloud`
dibuang.
