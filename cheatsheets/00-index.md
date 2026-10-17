# Cheatsheets — Índice

Metodología por clase de técnica. Pensados para repaso rápido antes y durante
el examen. **Sin referencias a máquinas** para que sirvan como material genérico
y publicable.

| # | Cheatsheet | Foco |
|---|------------|------|
| 01 | [Recon & Enumeration](01-recon-enumeration.md) | Puertos, directorios, vhosts/subdominios, fingerprinting |
| 02 | [SQL Injection](02-sql-injection.md) | Union, error, blind, auth bypass, sqlmap, dump+crack |
| 03 | [XSS](03-xss.md) | Reflejado, stored, blind, exfiltración vía JS |
| 04 | [File Upload](04-file-upload.md) | Bypass de filtros, extensiones, polyglots, content-type |
| 05 | [LFI / Path Traversal / File Read](05-lfi-traversal.md) | Lectura arbitraria, wrappers, log poisoning, RCE |
| 06 | [SSRF](06-ssrf.md) | Detección, bypass de filtros localhost, exfiltración, cloud |
| 07 | [SSTI](07-ssti.md) | Detección por motor, Jinja2/Twig, RCE |
| 08 | [Command Injection](08-command-injection.md) | Operadores, blind, bypass de filtros |
| 09 | [API & Auth Flaws](09-api-auth.md) | IDOR, mass assignment, JWT, enum de endpoints, side-channel |
| 10 | [Hash Cracking](10-hash-cracking.md) | Identificación, modos hashcat/john, wordlists |
| 11 | [PrivEsc Linux](11-privesc-linux.md) | sudo, SUID, capabilities, cron, Docker, kernel |
| 12 | [Shells & Pivoting](12-shells-pivoting.md) | Reverse shells, upgrade TTY, port forwarding, túneles |
| 13 | [XXE](13-xxe.md) | XML external entity: file read, SSRF, blind/OOB, SVG/DOCX |

## Flujo mental CWEE (grey-box)

```
1. Entiende la app  ──► lee el código fuente si lo tienes (grey-box)
2. Mapea entradas   ──► cada parámetro, header, cookie, ruta
3. Identifica sinks ──► dónde termina la entrada (query, eval, fs, template, exec, http)
4. Prueba la clase  ──► elige el cheatsheet según el sink
5. Encadena         ──► vuln web → foothold → privesc
6. Documenta        ──► writeup + añade técnica nueva al cheatsheet
```

> En CWEE casi siempre tienes el **código fuente**: prioriza el análisis
> estático (buscar sinks peligrosos) sobre el fuzzing a ciegas.
