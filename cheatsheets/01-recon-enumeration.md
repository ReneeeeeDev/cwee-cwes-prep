# Recon & Enumeration

## Escaneo de puertos

```bash
# Descubrimiento rápido de puertos abiertos (todos)
ports=$(nmap -p- --min-rate=1000 -T4 $IP | grep '^[0-9]' | cut -d'/' -f1 | tr '\n' ',' | sed 's/,$//')
# Enumeración de servicios/scripts sobre los puertos encontrados
nmap -p$ports -sC -sV $IP

# UDP (lento, usar cuando TCP no da pistas: IPMI/623, SNMP/161, etc.)
sudo nmap --min-rate=5000 -sU $IP
```

Tras encontrar un redirect a un dominio (`http://algo.htb/`), añádelo a hosts:

```bash
echo "$IP algo.htb" | sudo tee -a /etc/hosts
```

## Enumeración web de contenido

```bash
# Directorios
ffuf -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt:FUZZ \
     -u "http://algo.htb/FUZZ" -ic

# Con extensiones (ajusta según stack: php, asp, py, html, bak, txt)
ffuf -w WORDLIST:FUZZ -u "http://algo.htb/FUZZ" -ic -e .php,.bak,.txt

# gobuster equivalente
gobuster dir -u http://$IP -w /usr/share/wordlists/dirb/common.txt -x php
```

## Virtual hosts y subdominios (fuzzing por Host header)

```bash
ffuf -w /usr/share/seclists/Discovery/DNS/bitquark-subdomains-top100000.txt:FUZZ \
     -H "Host: FUZZ.algo.htb" -u http://algo.htb

# Filtra el baseline (mismo tamaño/words = falso positivo). Ej: -fw 20  o  -fs N
ffuf -w WORDLIST:FUZZ -H "Host: FUZZ.algo.htb" -u http://algo.htb -fw 20
```

> **Truco:** cuando todos los resultados devuelven el mismo tamaño, filtra por
> `-fw` (words), `-fs` (size) o `-fc` (code). Un resultado distinto (p.ej. 401)
> suele ser el objetivo real.

## Fingerprinting del stack

- `Server:` header → Apache / nginx / **Werkzeug (Flask/Python)** / **Gunicorn (Python)** / IIS.
- Errores verbosos → framework (Laravel, Django, Express, Spring).
- Cookies → `PHPSESSID`, `laravel_session`, `connect.sid` (Express), `JSESSIONID`.
- Archivos típicos: `robots.txt`, `/.git/`, `/download` (source), `composer.json`,
  `package.json`, `.env`, `web.config`.

## Checklist de recon

- [ ] Todos los puertos TCP + UDP relevantes
- [ ] Versiones de servicio → buscar CVE conocidos
- [ ] vhosts / subdominios
- [ ] Directorios y archivos ocultos / backups (`.bak`, `~`, `.old`)
- [ ] `.git` expuesto → `git-dumper`
- [ ] Source code disponible para descargar (grey-box)
- [ ] Mapea cada formulario, parámetro, header y cookie
