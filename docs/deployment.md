# Manual Deployment M03

## Environment

| Komponen | Nilai |
|---|---|
| Provider | AWS EC2, region ap-southeast-1 (Singapura) |
| Instance | t3.micro (2 vCPU, sekitar 1 GB RAM), disk 20 GB gp3 |
| OS | Ubuntu Server 24.04 LTS |
| Catatan | Modul memakai 1 vCPU; Gunicorn tetap dikonfigurasi 1 worker agar sesuai constraint RAM |

## Application Path
`/srv/cc-m03`

## Service
`cc-m03.service` (systemd), dijalankan sebagai user `cloudstudent`

## Backend
`127.0.0.1:8000` (Gunicorn, 1 worker, 2 thread)

## Reverse Proxy
Caddy pada port 80, meneruskan request ke `127.0.0.1:8000`

## Public Endpoint
`http://<PUBLIC_IP>/`

## Server Hardening

- User non-root `cloudstudent` dengan sudo
- `PermitRootLogin no`, `PasswordAuthentication no`, `AllowUsers cloudstudent`
- SSH hanya dengan key Ed25519
- Firewall berlapis: AWS Security Group dan UFW (OpenSSH dan 80/tcp)
- Swap 1 GB sebagai safety buffer

## Update Procedure

```bash
cd /srv/cc-m03
git pull
source .venv/bin/activate
python -m pip install -r requirements.txt
sudo systemctl restart cc-m03
sudo systemctl status cc-m03 --no-pager
curl -fsS http://127.0.0.1:8000/health
curl -fsS http://127.0.0.1/health
```

Setelah itu uji dari client eksternal (browser pada jaringan lain).

## Logs

```bash
sudo journalctl -u cc-m03 -n 50 --no-pager
sudo journalctl -u caddy -n 50 --no-pager
```

## Rollback Dasar

```bash
git log --oneline --decorate -10
git checkout m03
sudo systemctl restart cc-m03
git switch main
```

## Catatan AWS

- Security Group dan UFW harus konsisten: port yang dibuka di salah satu lapisan juga harus dibuka di lapisan lainnya.
- IP publik EC2 berubah ketika instance dihentikan lalu dinyalakan. Elastic IP dipakai mulai M04 agar alamat tetap.
- Instance dihentikan (bukan dihapus) di antara sesi praktikum untuk mengendalikan biaya.
