# Entorno ofensivo

## Imprescindibles
- **Burp Suite** (Community basta; Pro ideal para el examen) + extensiones:
  Logger++, JWT Editor, Param Miner, Autorize, Turbo Intruder.
- **ffuf** / gobuster — fuzzing de contenido y vhosts.
- **sqlmap** — confirmación/automatización de SQLi.
- **hashcat** + **john** — cracking.
- **SecLists** — `/usr/share/seclists` (wordlists).
- **nmap**.
- **proxychains4** + **chisel** — pivoting.
- **jwt_tool** — ataques a JWT.

## Scripts de post-explotación (descargar al día)
- linpeas: https://github.com/peass-ng/PEASS-ng
- pspy: https://github.com/DominicBreuker/pspy
- GTFOBins (referencia): https://gtfobins.github.io/

## Apps vulnerables para practicar en local (Docker)
```bash
# OWASP Juice Shop
docker run --rm -p 3000:3000 bkimminich/juice-shop
# DVWA
docker run --rm -p 8080:80 vulnerables/web-dvwa
# bWAPP
docker run --rm -p 8081:80 raesene/bwapp
```

## Setup rápido de wordlists
```bash
sudo apt install seclists
# rockyou
gzip -d /usr/share/wordlists/rockyou.txt.gz 2>/dev/null
```

## Buenas prácticas durante el examen
- Toma notas y screenshots **mientras** avanzas (no al final).
- Guarda cada request/response relevante (Burp → Save items).
- Comando exacto + output = PoC reproducible para el informe.
- Controla el tiempo: fija checkpoints por objetivo.
