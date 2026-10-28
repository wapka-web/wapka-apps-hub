# Middleware and hooks

Middleware runs before the route handler. Hooks run around the whole request.

## Built-in middleware

```lua
app:use("cors")   -- CORS headers; handles OPTIONS
app:use("json")   -- parses application/json into ctx.body
app:use("form")   -- parses application/x-www-form-urlencoded into ctx.body
app:use("auth")   -- 401 JSON when env.is_user is false
app:use("admin")  -- 403 JSON when env.is_admin is false
app:use("csrf")   -- verifies the CSRF token on unsafe methods
```

`app:use(...)` is global for the whole app.

## Route-level middleware

```lua
app:post("/api/items", handler, {
    middleware = "json"
})

app:post("/admin/save", handler, {
    middleware = { "auth", "admin", "form" }
})
```

Route middleware runs after global middleware. The `csrf` middleware is enabled by default for every route; exempt a specific route with:

```lua
app:post("/webhook", handler, { csrf = false })
```

## CSRF

Generate a token in Lua and pass it to the template:

```lua
local token = csrf_token()
return ctx:render("form", { csrf_token = token })
```

The form posts it as a field named `csrf_token`:

```twig
<input type="hidden" name="csrf_token" value="{{ csrf_token }}">
```

The `csrf` middleware accepts the token from `ctx.body.csrf_token`, `req.post.csrf_token` or `req.query.csrf_token`. It only applies to non-GET/HEAD/OPTIONS requests.

## Hooks

```lua
app:on("before", function(ctx) end)
app:on("after", function(ctx, result) end)
app:on("error", function(err, ctx) end)
app:on("response", function(ctx) end)
```

Request order:

```text
before hooks
  → global middleware
  → route middleware
  → handler
  → after hooks
  → response is built
  → response hooks
  → default handler if the body is still empty
```

## Custom middleware

```lua
app:use(function(ctx)
    ctx:set_header("x-powered-by", "wapka-visual")
end)
```

A middleware can stop the request by setting `ctx._stopped = true`, but prefer returning a normal response from a route handler.

## Default handler

```lua
app:set_default(function(ctx)
    return ctx:text("This page has no content yet.")
end)
```

## Related

- `routing` — route options and groups.
- `context` — body parsing effects.
- `auth` — auth/admin gates.
- `form` — CSRF in a real form flow.
