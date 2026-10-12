# Requirements — WordRoom

## Problem Statement
Guru, dosen, dan komunitas belajar membutuhkan aktivitas evaluasi interaktif yang dapat diikuti banyak orang sekaligus tanpa instal aplikasi. Sistem menyediakan kuis cepat berlomba berbasis browser, di mana pemain masuk ke ruangan lewat kode dan menjawab pertanyaan secara bersamaan, sehingga partisipasi meningkat dan hasilnya langsung terlihat.

## Target Users
- **Host** (guru, dosen, ketua komunitas): membuat ruangan dan membagikan kode.
- **Pemain** (siswa, mahasiswa, anggota komunitas): masuk lewat kode dari HP atau laptop.

## User Needs
- Sebagai host, saya ingin membuat ruangan kuis dengan satu kode, sehingga peserta bisa bergabung dengan cepat.
- Sebagai pemain, saya ingin masuk tanpa membuat akun, sehingga kuis bisa dimulai tanpa persiapan panjang.
- Sebagai host, saya ingin melihat skor langsung, sehingga hasil kelas bisa dibahas saat itu juga.

## Functional Requirements

### M03 (baseline)
- FR-01 Sistem menampilkan halaman utama.
- FR-02 Sistem menyediakan endpoint `/health` yang mengembalikan HTTP 200 saat sehat.
- FR-03 Sistem menampilkan metadata aplikasi melalui `/api/info`.

### Backlog (pertemuan berikutnya)
- FR-04 Host dapat membuat ruangan dan mendapat kode unik.
- FR-05 Pemain dapat masuk ruangan dengan kode dan nama.
- FR-06 Pertanyaan pilihan ganda tampil bersamaan untuk semua pemain.
- FR-07 Jawaban benar mendapat poin; jawaban lebih cepat mendapat poin lebih besar.
- FR-08 Papan skor tampil di akhir setiap ronde.
- FR-09 Bank soal tersimpan di database.

## Non-Functional Requirements
- NFR-01 Backend hanya listen pada loopback (`127.0.0.1`).
- NFR-02 Aplikasi dikelola oleh systemd.
- NFR-03 Request publik masuk melalui reverse proxy.
- NFR-04 Secret tidak disimpan pada Git repository.
- NFR-05 Health endpoint mengembalikan HTTP 200 saat sehat.
- NFR-06 Deployment dapat direproduksi dari repository.
- NFR-07 Arsitektur sesuai server 1 GB RAM.

## Scope

### In scope M03
repository, requirements, architecture, ADR, baseline Flask app, hardening SSH dasar, Gunicorn, systemd, Caddy, deployment HTTP publik.

### Out of scope M03
database, Docker, Infrastructure as Code, CI/CD, TLS/domain, koneksi real-time, akun pengguna, scaling horizontal.

## Constraints
- 1 GB RAM (instance kecil)
- Ubuntu Server 24.04 LTS
- Public IPv4
- Biaya dikendalikan (resource dihapus atau dihentikan saat tidak dipakai)

## Acceptance Criteria M03
- [ ] `http://<PUBLIC_IP>/` dapat dibuka dari jaringan lain
- [ ] `/health` mengembalikan HTTP 200
- [ ] Gunicorn berjalan sebagai service systemd
- [ ] Backend hanya listen pada `127.0.0.1:8000`
- [ ] SSH hanya dengan key, login root dinonaktifkan
- [ ] Firewall aktif
- [ ] Evidence tersimpan dan tidak berisi secret
