# Hash Cracking

## Identificar el hash

```bash
hashid '$apr1$bMoRBJOg$igG8WBtQ1xYDTQdLjSWZQ/'
hash-identifier
# o por forma: longitud, prefijo ($1$, $2y$, $apr1$, $6$, {SHA}), charset
```

| Prefijo / forma | Tipo | hashcat -m | john --format |
|-----------------|------|-----------|---------------|
| 32 hex | MD5 | 0 | raw-md5 |
| 40 hex | SHA1 | 100 | raw-sha1 |
| 64 hex | SHA256 | 1400 | raw-sha256 |
| `$1$` | md5crypt | 500 | md5crypt |
| `$apr1$` | Apache MD5 (.htpasswd) | 1600 | — |
| `$2a$/$2y$` | bcrypt | 3200 | bcrypt |
| `$6$` | sha512crypt | 1800 | sha512crypt |
| `$5$` | sha256crypt | 7400 | sha256crypt |
| WPA/JWT/NetNTLM | varios | 22000 / 16500 / 5600 | — |

## hashcat

```bash
# Diccionario
hashcat -a 0 -m 1600 hash.txt /usr/share/wordlists/rockyou.txt

# Con reglas (muy recomendable)
hashcat -a 0 -m 0 hash.txt rockyou.txt -r /usr/share/hashcat/rules/best64.rule

# Fuerza bruta por máscara (ej. 8 dígitos)
hashcat -a 3 -m 0 hash.txt ?d?d?d?d?d?d?d?d

# Ver resultado
hashcat -m 1600 hash.txt --show
```

## john

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
john --show hash.txt

# Convertir formatos a john
ssh2john id_rsa > id_rsa.hash
zip2john file.zip > zip.hash
```

## Wordlists y mutación

```bash
# Generar wordlist desde el sitio objetivo
cewl http://victima.htb -w custom.txt

# Mutar con reglas
hashcat --stdout custom.txt -r best64.rule > mutated.txt
```

## Flujo típico CWEE

`SQLi/LFI` extrae hash → `hashid` → `hashcat -m <modo> rockyou` → credencial →
**reutilizar** en SSH / panel admin / otro servicio (password reuse es la norma
en estas máquinas).
