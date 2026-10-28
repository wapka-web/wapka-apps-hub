# Twig templates

Every view file is rendered through Twig, including CSS, JS, text, JSON and XML views. The template is sandboxed: no PHP functions, no methods, no filesystem.

## Page names, not paths

Use page names everywhere:

```lua
return ctx:render("home", { title = "Home" })
```

```twig
{% extends "layout" %}
{% include "card" with {'poster': poster} only %}
{% from "macros" import price %}
```

Do not write `views/home.twig`, `layout.twig` or `logic/home.lua` inside Lua or Twig.

## Autoescape follows the page type

| Page type | Strategy |
|---|---|
| 0 HTML | `html` |
| 1 Text | `html` |
| 2 CSS | `css` |
| 3 JS | `js` |
| 4 JSON | `html` |
| 5 XML | `html` |

Use `|raw` only for content you fully trust. Normal output is escaped.

## Allowed tags

```text
if elseif else endif
for endfor
set
include
extends
block endblock
macro endmacro
spaceless endspaceless
verbatim endverbatim
apply endapply
autoescape endautoescape
flush
from
import
```

## Allowed filters

```text
escape e raw upper lower capitalize title trim length reverse first last
join split sort date date_modify format replace number_format abs round
ceil floor json_encode url_encode striptags nl2br slice default keys batch
column filter map reduce find
```

## Allowed functions

```text
range cycle date max min random date_format block attribute template_from_string
```

## What Twig does not have

There are no implicit `site`, `csrf_token`, `flash` or `user` variables. Pass everything from Lua:

```lua
app:get("/contact", function(ctx)
    return ctx:render("contact", {
        csrf_token = csrf_token(),
        error = ctx:flash("error"),
        success = ctx:flash("success")
    })
end)
```

```twig
<form method="post" action="/contact">
    <input type="hidden" name="csrf_token" value="{{ csrf_token }}">
    {% if error %}<p class="error">{{ error }}</p>{% endif %}
    <input name="name" value="">
    <button type="submit">Send</button>
</form>
```

`csrf_token()` is a Lua global, not a Twig function. `flash()` is a `ctx:` method, not a Twig function.

## Example

```twig
{% extends "layout" %}

{% block content %}
<section class="hero">
    <h1>{{ title }}</h1>
    {% if featured|length > 0 %}
        {% for item in featured %}
            {% include "card" with {'item': item} only %}
        {% endfor %}
    {% endif %}
</section>
{% endblock %}
```

## Related

- `form` — CSRF and flash in a real form.
- `responses` — passing data from handlers.
- `dataset` — shaping data for a template.
- `rules` — common Twig mistakes.
