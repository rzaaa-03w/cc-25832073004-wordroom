# WordRoom

Kuis cepat berlomba berbasis browser. Host membuat ruangan, pemain masuk lewat kode, lalu pertanyaan muncul bersamaan untuk semua pemain. Jawaban benar mendapat poin, dan jawaban yang lebih cepat mendapat poin lebih besar.

Proyek semester mata kuliah **Cloud Computing (PTI 2802)**, Institut Pendidikan Indonesia.

## Status

| Pertemuan | Capaian | Status |
|---|---|---|
| M03 | Server pertama, aplikasi dasar, deployment HTTP | Selesai (tag `m03`) |
| M04 | Domain dan HTTPS | Direncanakan |
| M05 | Database untuk soal, ruangan, skor | Direncanakan |
| M06 | Container | Direncanakan |
| M09 | CI/CD | Direncanakan |

Fitur kuis belum diimplementasikan. Versi M03 hanya berisi baseline aplikasi dan infrastruktur.

## Endpoint (M03)

| Path | Fungsi |
|---|---|
| `/` | Halaman utama |
| `/health` | Health check, mengembalikan HTTP 200 dan JSON status |
| `/api/info` | Metadata aplikasi |

## Arsitektur

```
Internet -> Security Group + UFW -> Caddy (:80) -> Gunicorn (127.0.0.1:8000) -> Flask
```

- Server: AWS EC2, Ubuntu Server 24.04 LTS, 1 GB RAM, region ap-southeast-1
- Reverse proxy: Caddy
- Application server: Gunicorn (1 worker, 2 thread)
- Service manager: systemd (`cc-m03.service`)
- Backend hanya listen pada `127.0.0.1`, tidak terekspos langsung

Detail ada di [`docs/architecture.md`](docs/architecture.md), [`docs/requirements.md`](docs/requirements.md), dan [`docs/adr/`](docs/adr/).

## Menjalankan secara lokal

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
flask --app app run
```

Buka `http://127.0.0.1:5000/`.

## Deployment (ringkas)

1. Buat server Ubuntu 24.04 dan buka hanya port 22 (dibatasi ke satu IP) dan 80.
2. Buat user non-root, SSH hanya dengan key, nonaktifkan login root dan password.
3. Aktifkan firewall UFW (OpenSSH dan 80/tcp) dan tambahkan swap 1 GB.
4. Instal Python, Git, dan Caddy.
5. Clone repository ke `/srv/cc-m03` memakai deploy key, buat virtual environment, dan instal dependensi.
6. Jalankan Gunicorn sebagai service systemd, lalu atur Caddy sebagai reverse proxy ke `127.0.0.1:8000`.

## Keamanan

- Login SSH hanya dengan key; `PermitRootLogin no` dan `PasswordAuthentication no`.
- Firewall berlapis: Security Group (jaringan) dan UFW (host).
- Secret tidak disimpan di repository (`.env` diabaikan lewat `.gitignore`).
- Deploy key GitHub hanya untuk repository ini dan dicabut di akhir sesi.

## Evidence

Bukti konfigurasi dan pengujian M03 ada di [`docs/evidence/m03/`](docs/evidence/m03/): konfigurasi OS, memori, disk, firewall, SSH efektif, status service, port yang listen, dan hasil health check.

## Keterbatasan saat ini

- Hanya HTTP, belum HTTPS
- Satu server tanpa redundansi
- Belum ada database
- Deployment masih manual
- IP publik berubah saat server dihentikan dan dinyalakan lagi

## Roadmap

HTTPS dan domain (M04), database untuk bank soal dan skor (M05), container (M06), CI/CD (M09), kemudian ruangan real-time dan uji beban.
