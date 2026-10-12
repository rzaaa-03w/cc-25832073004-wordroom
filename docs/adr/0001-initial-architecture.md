# ADR-0001: Initial Deployment Architecture

## Status
Accepted

## Context
WordRoom (kuis cepat berlomba) dideploy pada satu server Ubuntu Server 24.04 dengan RAM 1 GB dan IPv4 publik. Proyek akan berkembang sampai akhir semester, dan fondasinya harus aman, ringan, dan mudah dijelaskan.

## Decision
- Flask sebagai web framework
- Gunicorn (1 worker, 2 thread) sebagai application server
- systemd untuk mengelola service
- Caddy sebagai reverse proxy
- UFW sebagai firewall host (di samping Security Group)
- GitHub sebagai repository

## Alternatives Considered
1. Node.js + PM2: tidak dipilih karena Python lebih dekat dengan materi dan lebih ringan dijelaskan.
2. NGINX + Gunicorn: tidak dipilih karena Caddy lebih sederhana dan siap untuk HTTPS otomatis di M04.
3. Docker: ditunda karena containerisasi adalah capaian M06.
4. PHP-FPM: tidak dipilih karena tidak sejalan dengan stack Python.

## Rationale
Stack ini ringan untuk RAM 1 GB, mudah dipahami, dan merepresentasikan arsitektur deployment nyata. Gunicorn hanya listen pada loopback sehingga satu-satunya pintu masuk publik adalah Caddy.

## Consequences

### Positive
- Footprint kecil
- Setiap komponen dapat dijelaskan
- Jalur menuju HTTPS (M04) dan container (M06) jelas

### Negative
- Deployment manual
- Satu server tanpa redundansi
- Kebutuhan real-time (WebSocket) akan menuntut penyesuaian worker dan konfigurasi proxy

## Review Trigger
Tinjau ulang pada M06/M07, atau saat fitur real-time mulai dikerjakan.
