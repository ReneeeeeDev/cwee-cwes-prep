# Roadmap de preparación CWES → CWEE

## Las dos certificaciones (aclaración)

| | **CWES** | **CWEE** |
|---|---|---|
| Nombre | Certified Web Exploitation **Specialist** | Certified Web Exploitation **Expert** |
| Nivel | Intermedio (bug bounty / web pentester) | Avanzado (experto) |
| Origen | Evolución de **CBBH** (desde oct 2025) | Cert propia avanzada |
| Path HTB Academy | **Web Penetration Tester** | **Senior Web Penetration Tester** |
| Enfoque | Identificar y explotar vulns web comunes | Técnicas avanzadas + **encadenar** vulns + **whitebox** (source) |
| Examen | Pentest web guiado + **informe** profesional | Escenarios complejos tipo real + **informe** profesional |

> Orden recomendado: **CWES primero**, luego CWEE. CWEE asume que ya dominas
> todo lo de CWES y añade variantes avanzadas, bypass de defensas y revisión de
> código fuente.
>
> Si ya tienes CBBH, estás auto-upgradeado a CWES: ve directo a CWEE.

Ambos exámenes exigen **redactar un informe**. Practica el reporting desde ya
(por eso este repo prioriza writeups propios).

---

## Fase 0 — Entorno
Ver [`tools.md`](tools.md). Ten listos: Burp Suite, ffuf, sqlmap, hashcat/john,
SecLists, revshells, proxychains/chisel.

## Fase 1 — CWES (base) · HTB Academy "Web Penetration Tester"
Módulos clave a dominar:
- Web requests, Web apps, Web proxies (Burp)
- Information Gathering (Web), **Web Fuzzing**
- JavaScript Deobfuscation, **XSS**, **SQL Injection**, SQLMap, **Command Injection**
- **File Upload Attacks**, **Server-side Attacks**, Login Brute Forcing, Broken Authentication, **File Inclusion**
- **API Attacks**, **GraphQL**, Attacking Common Applications

**Máquinas de este repo que refuerzan CWES** (todas las Easy + algunas Medium):
`Alert, Usage, Headless, TwoMillion, OpenSource, GoodGames, Cap` → cubren XSS,
SQLi, upload, LFI/traversal, command injection, API, IDOR.

## Fase 2 — CWEE (avanzado) · "Senior Web Penetration Tester"
Temas que suben de nivel respecto a CWES:
- **Advanced SQL Injection** (bypass de WAF, blind avanzado, OOB)
- **Advanced XSS & CSRF** (CSP bypass, mutation XSS, chaining)
- **Server-Side Attacks**: **SSRF**, **SSTI**, **XXE**
- **(In)secure Deserialization** (PHP, Python pickle, Java, .NET)
- **Whitebox attacks** / code review (te dan el código fuente → encuentra el sink)
- **Attacking authentication / JWT avanzado**
- **Blind / OOB** data exfiltration

**Máquinas de este repo orientadas a CWEE:** `GoodGames` (SSTI), `Forge` (SSRF),
`Timing` (LFI + chaining), + las que practiques en whitebox.

---

## PortSwigger Web Security Academy — labs por tema

Gratis y excelentes. Mapea cada tema a su cheatsheet de este repo. Haz **todos**
los "apprentice" y "practitioner"; los "expert" son oro para CWEE.

| Tema PortSwigger | Cheatsheet | Prioridad |
|---|---|---|
| SQL injection | [02](../cheatsheets/02-sql-injection.md) | CWES+CWEE |
| Cross-site scripting | [03](../cheatsheets/03-xss.md) | CWES+CWEE |
| Cross-site request forgery (CSRF) | — | CWEE |
| Clickjacking | — | CWES |
| CORS | [09](../cheatsheets/09-api-auth.md) | CWEE |
| XXE injection | — (añadir) | CWEE |
| SSRF | [06](../cheatsheets/06-ssrf.md) | CWEE |
| OS command injection | [08](../cheatsheets/08-command-injection.md) | CWES+CWEE |
| Server-side template injection | [07](../cheatsheets/07-ssti.md) | CWEE |
| Path traversal | [05](../cheatsheets/05-lfi-traversal.md) | CWES |
| Access control / IDOR | [09](../cheatsheets/09-api-auth.md) | CWES |
| Authentication | [09](../cheatsheets/09-api-auth.md) | CWES |
| File upload vulnerabilities | [04](../cheatsheets/04-file-upload.md) | CWES |
| JWT attacks | [09](../cheatsheets/09-api-auth.md) | CWES+CWEE |
| Insecure deserialization | — (añadir) | CWEE |
| Web LLM attacks | — | opcional |
| GraphQL API vulnerabilities | [09](../cheatsheets/09-api-auth.md) | CWES |
| Business logic vulnerabilities | — | CWES |
| Information disclosure | [01](../cheatsheets/01-recon-enumeration.md) | CWES |
| Race conditions | — (añadir) | CWEE |
| HTTP request smuggling | — | CWEE (avanzado) |
| OAuth authentication | — | CWEE |
| WebSockets | — | opcional |
| Web cache poisoning / deception | — | CWEE (avanzado) |
| Prototype pollution | — | CWEE |

> **Pendiente de crear** (temas CWEE sin cheatsheet aún): XXE, Deserialization,
> CSRF, Race conditions, Prototype pollution, Request smuggling. Los iré
> añadiendo a `cheatsheets/` conforme los estudie.

---

## Fortresses de HTB (lo que mencionaste)
Entornos grandes multi-flag, buenos para encadenar y para resistencia.
- **Akerva** (lo llamaste "arkeva") — muy orientado a web, deserialización, retos encadenados. Top para CWEE.
- **Jet** — web + AD-ish.
- **Context** — web.
- **Faraday** — mixto.

> Para CWEE prioriza **Akerva** por su carga web/deserialización.

## Challenges de HTB (categoría Web)
Hazlos en orden de dificultad. Cubren vulns que no siempre aparecen en máquinas:
- Web (todos los que puedas): JWT, deserialización, SSTI, prototype pollution,
  NoSQLi, request smuggling.
- Algunos **Crypto** y **Reversing** ligeros ayudan a leer tokens/ofuscación.

## Otros recursos
- **PentesterLab** (Web for Pentester, badges de código).
- **PortSwigger "Mystery lab"** para simular examen a ciegas.
- Blog de PortSwigger / investigaciones de James Kettle (CWEE level).
- HackTricks (web) como índice de técnicas.

---

## Lo que se te está escapando (recomendaciones)

1. **Reporting.** Ambos exámenes se aprueban con un **informe**, no solo con la
   shell. Practica: plantilla de informe (resumen ejecutivo, hallazgos con
   CVSS, PoC reproducible, impacto, remediación). → crear `resources/report-template.md`.
2. **Whitebox / code review.** CWEE te da el **código fuente**. Entrena a leer
   PHP/Python/Node y localizar sinks (`eval`, `system`, `unserialize`,
   `render_template_string`, queries concatenadas). Tus writeups deberían
   incluir el fragmento vulnerable del código.
3. **Deserialización y XXE.** No están cubiertos por tus máquinas actuales y son
   núcleo de CWEE. Añade máquinas/labs específicos.
4. **Metodología cronometrada.** Simula el examen con límite de tiempo; el
   cuello de botella suele ser el tiempo, no la técnica.
5. **Gestión de notas/evidencias.** Usa un sistema (Obsidian/CherryTree/Joplin)
   con screenshots y comandos exactos → se convierte en el informe casi solo.
6. **Lab local reproducible.** Docker con apps vulnerables (DVWA, Juice Shop,
   bWAPP) para practicar payloads sin gastar tiempo de VPN.
7. **Variación de máquinas.** Tus 10 son sólidas pero repiten patrones
   (SQLi→hash→SSH). Añade variedad: deserialización, SSTI avanzado, JWT, race
   conditions, 2nd-order injection.
8. **No publiques writeups de máquinas activas.** Respeta el embargo de HTB:
   solo publica writeups de máquinas **retiradas**. (Todas las tuyas lo están,
   pero tenlo presente al añadir nuevas.)

### Fuentes
- [HTB: Introducing CWES](https://www.hackthebox.com/blog/htb-cwes-announcement)
- [HTB Academy: transición CBBH → CWES](https://academy.hackthebox.com/news/the-transition-from-htb-cbbh-to-htb-cwes-has-officially-started)
- [HTB Roadmap: CWES & CWEE preparation tracks](https://roadmap.hackthebox.com/changelog/htb-cwes-and-htb-cwee-preparation-tracks)
- [Dark Reading: HTB launches CWEE](https://www.darkreading.com/cyber-risk/hack-the-box-launches-certified-web-exploitation-expert-as-demand-for-risk-mitigation-grows)
- [PortSwigger Web Security Academy](https://portswigger.net/web-security)
