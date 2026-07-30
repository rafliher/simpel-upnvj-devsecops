# SIMPEL UPNVJ — DevSecOps (CI/CD + hasil scan)

Aplikasi **sengaja rentan** (clone `simpel.upnvj.ac.id` di Node.js/Express + EJS) **plus pipeline DevSecOps lengkap**. Tiap push ke `main` menjalankan **5 tool keamanan** dalam mode **report-only** (pipeline selalu hijau — tujuannya melihat temuan, bukan nge-block).

> ⚠️ Aplikasi ini penuh kerentanan **dengan sengaja**. Jangan diekspos ke internet. Source-only tanpa pipeline ada di repo `simpel-upnvj-vuln`.

## 5 tool & jenis scan

| Tool | Jenis | Yang ditemukan |
|---|---|---|
| **Semgrep** | SAST | SQLi, XSS, SSTI, command injection, path traversal, MD5, secret |
| **Trivy** | SCA + secret + misconfig | CVE dependensi, secret hardcoded, misconfig Docker |
| **OWASP Dependency-Check** | SCA (CVE/NVD) | CVE pada ejs/lodash/jsonwebtoken/multer/axios |
| **SonarCloud** | SAST | (opsional — butuh repo public + `SONAR_TOKEN`) |
| **OWASP ZAP** | DAST | header keamanan hilang, XSS, cookie flag, dll (app di-boot ephemeral) |

## Di mana melihat alert-nya

Repo ini **private**, jadi tab **Security → Code scanning** butuh GitHub Advanced Security. Supaya alert tetap terlihat tanpa GHAS, tiap job:

1. **Menulis ringkasan temuan ke Job Summary** — buka **Actions → run `devsecops` terbaru**, scroll ke ringkasan tiap job (tabel Semgrep, Trivy secret/CVE, Dependency-Check, ZAP).
2. **Mengunggah laporan mentah sebagai Artifacts** — di halaman run yang sama, bagian **Artifacts**: `semgrep-report`, `trivy-report`, `depcheck-report` (HTML), `zap-report` (HTML/JSON/MD).

> Ingin alert muncul di tab **Security**? Jadikan repo **public** (upload SARIF Semgrep otomatis aktif) atau nyalakan GitHub Advanced Security. SonarCloud juga aktif saat repo public + `SONAR_TOKEN` diisi.

## Menjalankan lokal

```bash
docker compose up -d --build      # http://localhost:8080  (login admin/admin123)
./verify.sh                       # bukti 18+ exploit berhasil
```

## Kerentanan

20 temuan memetakan OWASP Top 10 2025 — lihat tabel & lokasi di source (`routes/`, `lib/`, `server.js`). `verify.sh` membuktikan tiap exploit berhasil di app yang berjalan.
