# API & Auth Flaws

## Enumeración de endpoints de API

- Lee el **JS del frontend** (`inviteapi.min.js`, bundles): revela rutas
  (`/api/v1/...`), parámetros y lógica. Des-ofusca con beautifier.
- Patrones de versión: `/api/v1/`, `/api/v2/`, `/rest/`, `/graphql`.
- Métodos: prueba `GET`, `POST`, `PUT`, `DELETE`, `PATCH`, `OPTIONS` en cada ruta.
- Documentación expuesta: `/swagger`, `/api-docs`, `/openapi.json`, `/graphql` (introspection).

```bash
# Fuzz de endpoints de API
ffuf -w api-wordlist.txt:FUZZ -u http://victima/api/v1/FUZZ -mc all
```

## IDOR (Insecure Direct Object Reference)

Identificadores secuenciales/predecibles en la URL o body → cambia el ID y
accede a objetos de otros usuarios.

```
GET /api/user/1001   → cambia a 1000, 1002...
GET /data/0          → 0,1,2 (capturas, documentos, pcaps)
```

> Patrón: endpoint de "descarga" con ID numérico → iterar IDs → encontrar uno
> con datos sensibles (credenciales en una captura, documento ajeno).

## Mass assignment / parámetros ocultos

El backend acepta campos que el formulario no muestra. Añádelos en el body:

```json
{ "username": "x", "email": "x@x", "role": "admin", "is_admin": true }
```

> Patrón: página de "editar perfil" que acepta un `role`/`isAdmin` oculto →
> auto-escalada a administrador.

## Enumeración de usuarios por side-channel

- **Mensajes distintos** ("usuario no existe" vs "password incorrecta").
- **Diferencia de tiempo**: usuarios válidos tardan más (se hace hash de la pass).
  Script que mide latencia por usuario → identifica cuentas reales.

## JWT

```
# Debilidades comunes
alg: none              → quitar firma
alg: HS256 con secret débil → crackear con hashcat (-m 16500) / jwt_tool
Confusión RS256→HS256  → firmar con la clave pública como secreto HMAC
```

```bash
jwt_tool TOKEN -C -d wordlist.txt      # crack del secreto
hashcat -m 16500 jwt.txt rockyou.txt
```

## Command injection en endpoints de API

Endpoints que generan artefactos (config VPN, PDF, thumbnails) suelen pasar
input a comandos → ver [08-command-injection.md](08-command-injection.md).

## Mitigación

Autorización por objeto (no solo autenticación), IDs no predecibles (UUID),
allowlist de campos en el binding, mensajes de error uniformes, tiempos
constantes, validación de `alg` y secretos fuertes en JWT.
