---
tags: [momot, kontrak, api]
---

# Kontrak API aplikasi (aplikasi ke cloud)

Kembali ke [peta](README.md). Rute `/v1/auth/*` dan `/v1/app/*` memakai
`Authorization: Bearer <tokenSesi>`. Semua balasan JSON.

## Umum dan akun

| Rute | Isi |
|---|---|
| `GET /health` | `{ok, waktu, versi, sejak}`; ikut memeriksa MySQL |
| `GET /v1/catalog` | Model, suara, bahasa, dan setelan bawaan |
| `POST /v1/auth/daftar` | `{email, sandi, nama}` menjadi `{token, user}` |
| `POST /v1/auth/masuk` | Nama pengguna atau email, plus sandi |
| `POST /v1/auth/keluar` | Cabut sesi ini |
| `GET`/`PATCH`/`DELETE /v1/auth/saya` | Profil, ganti nama, hapus akun (`{konfirmasi:"HAPUS"}`) |
| `POST /v1/auth/sandi` | `{lama, baru}`; mencabut sesi lain |
| `GET /v1/auth/ekspor` | Seluruh data akun sebagai JSON |

## Perangkat

| Rute | Isi |
|---|---|
| `GET`/`POST /v1/app/devices` | Daftar, atau daftarkan baru. POST membalas `device` dan `provisioning` |
| `GET`/`PATCH`/`DELETE /v1/app/devices/:id` | Satu perangkat; PATCH untuk `nama` |
| `POST /v1/app/devices/:id/retoken` | Token baru untuk ganti WiFi (khusus pemilik) |
| `PATCH /v1/app/devices/:id/config` | Ubah setelan; `rev` naik bila isinya berubah |
| `GET`/`POST /v1/app/devices/:id/jadwal` | Jadwal |
| `PATCH`/`DELETE /v1/app/jadwal/:id` | Satu jadwal |
| `GET`/`POST /v1/app/devices/:id/berbagi` | Berbagi ke akun lain |
| `DELETE /v1/app/devices/:id/berbagi/:userId` | Cabut berbagi |

## Akun lainnya

| Rute | Isi |
|---|---|
| `GET /v1/app/aktivitas?batas=` | Riwayat dan jumlah belum dibaca |
| `POST /v1/app/aktivitas/dibaca` | Tandai semua dibaca |
| `GET`/`POST /v1/app/ingatan`, `PATCH`/`DELETE /v1/app/ingatan/:id` | Ingatan milik akun |
| `GET`/`POST /v1/app/jurnal`, `PATCH`/`DELETE /v1/app/jurnal/:id` | Jurnal |

## Peran berbagi

`pemilik`, `atur` (ubah setelan dan jadwal), dan `lihat`. Bila pengguna yang
bukan pemilik memanggil `DELETE`, ia hanya melepaskan dirinya dari perangkat.
Rute yang tidak boleh diakses membalas **404, bukan 403**, supaya penebak
tidak tahu id itu ada.

Bentuk perangkat: `{id, nama, online, lastSeen, rssi, ip, firmware, ssid,
bateraiMv, bateraiPersen, rev, config, dibuat, peran}`.
