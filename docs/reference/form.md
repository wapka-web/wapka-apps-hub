# Handle a POST form

Use this pattern for browser forms: render with a CSRF token, validate on POST, flash a message, redirect.

## Setup

```lua
local app = framework()

app:use("form")
app:use("csrf")
```

The `form` middleware parses URL-encoded bodies into `ctx.body`. Ordinary form fields are also available in `req.post`.

## GET — render the form

```lua
app:get("/contact", function(ctx)
    return ctx:render("contact", {
        csrf_token = csrf_token(),
        error = ctx:flash("error"),
        success = ctx:flash("success"),
        values = {}
    })
end)
```

```twig
{% extends "layout" %}
{% block content %}
<form method="post" action="/contact">
    <input type="hidden" name="csrf_token" value="{{ csrf_token }}">

    {% if error %}<p class="error">{{ error }}</p>{% endif %}
    {% if success %}<p class="success">{{ success }}</p>{% endif %}

    <label>Name
        <input name="name" value="{{ values.name|default('') }}">
    </label>
    <label>Email
        <input name="email" type="email" value="{{ values.email|default('') }}">
    </label>
    <label>Message
        <textarea name="message">{{ values.message|default('') }}</textarea>
    </label>

    <button type="submit">Send</button>
</form>
{% endblock %}
```

## POST — validate and redirect

```lua
app:post("/contact", function(ctx)
    local body = type(ctx.body) == "table" and ctx.body or req.post
    local v = validator(body, {
        name = "required|string|min:3|max:80",
        email = "required|email",
        message = "required|string|min:10"
    })

    if v:fails() then
        ctx:flash("error", v:first())
        return ctx:redirect("/contact", 303)
    end

    local clean = v:valid()
    local saved = api.dataset.create("messages", clean)
    if not saved then
        ctx:flash("error", "Could not save the message")
        return ctx:redirect("/contact", 303)
    end

    ctx:flash("success", "Thanks, " .. clean.name)
    return ctx:redirect("/contact", 303)
end)
```

## Rules

- Always render a hidden `csrf_token` for browser POSTs.
- Use `app:use("csrf")` globally. Use `csrf = false` on a route only for webhooks or machine-to-machine calls.
- Validate before writing.
- On success or validation failure, redirect. This prevents duplicate submissions.
- `api.dataset.create` requires an authenticated user (`env.is_user`). A public contact form cannot write to the dataset directly; use an authenticated flow or an external endpoint.
- Do not claim `api.users.login` creates a session; it does not.

## Related

- `twig` — template syntax and escaping.
- `validation` — all validator rules.
- `middleware` — CSRF and body parsing.
- `responses` — flash and redirect behavior.
