# CWEE / CWES Preparation

Repositorio personal de preparación para las certificaciones de explotación web
de HackTheBox. Contiene **writeups propios**, **cheatsheets de metodología** y un
**roadmap de estudio** que combina máquinas de HTB, laboratorios de PortSwigger,
fortresses y challenges.

> **Nota de alcance:** CWEE (Certified Web Exploitation Expert) es una
> certificación 100% web y de caja blanca/gris (grey-box, con código fuente).
> No existe una cert llamada exactamente "CWES" en el catálogo de HTB — revisa
> [`resources/roadmap.md`](resources/roadmap.md) para ver la aclaración y cómo
> ajustar la ruta si te refieres a CBBH, CPTS u otra.

---

## Estructura

```
.
├── cheatsheets/              # Metodología por técnica (SIN referencias a máquinas)
├── writeups/
│   ├── easy/                 # Mis writeups de máquinas fáciles
│   ├── medium/               # Mis writeups de máquinas medias
│   └── _official-reference/  # PDFs oficiales HTB — GITIGNORED, uso local
├── resources/
│   ├── roadmap.md            # Qué estudiar y en qué orden
│   └── tools.md              # Setup del entorno ofensivo
├── progress.md               # Tracker de avance
└── README.md
```

## ⚠️ Importante sobre los PDFs oficiales

Los writeups oficiales de HTB son **contenido con copyright de pago**. Están en
`writeups/_official-reference/` y ese directorio está en `.gitignore`: **nunca**
se suben a GitHub. Úsalos solo como referencia local. Lo que publicas es:

- Tus **writeups propios** (redactados con tus palabras).
- Los **cheatsheets** (metodología genérica, sin spoilers de máquinas).

## Cómo trabajar este repo

1. Resuelve la máquina / lab por tu cuenta.
2. Compara con el PDF oficial (referencia local).
3. Escribe tu writeup en `writeups/` usando la plantilla.
4. Si aparece una técnica nueva, añádela al cheatsheet correspondiente.
5. Actualiza `progress.md`.

## Inventario actual de máquinas

| Máquina | Dif. | Técnicas web clave | PrivEsc |
|---------|------|--------------------|---------|
| Alert | Easy | Stored XSS, Arbitrary File Read | cron + script escribible |
| Usage | Easy | SQLi, bypass de filtro en file upload | creds en claro + sudo |
| Headless | Easy | Blind XSS, Command Injection | sudo |
| TwoMillion | Easy | API enumeration, Command Injection | CVE-2023-0386 |
| OpenSource | Easy | File upload + Directory Traversal (RCE) | git hook (cron root) + Docker |
| GoodGames | Easy | Union SQLi, SSTI | Docker breakout |
| Cap | Easy | IDOR, sniffing de pcap (FTP) | Linux capabilities |
| Timing | Medium | Side-channel user enum, LFI, file upload | sudo (Axel config) |
| Shibboleth | Medium | IPMI hash leak, Zabbix RCE | MySQL UDF |
| Forge | Medium | SSRF + bypass de filtro | sudo script → pdb |

_(El inventario sirve de índice; los detalles de resolución van en tus writeups.)_
