# Request, environment and context

The handler receives `ctx`. The same values are also available as globals inside page logic loaded with `loadpage`.

## `req` fields

```text
req.method      "GET", "POST", ...
req.url         full request URL
req.uri         request URI including query string
req.path        normalized path without query string
req.args        raw query string
req.get         query-string table
req.query       alias of req.get
req.post        form-encoded POST table
req.headers     request headers table (lower-case keys)
req.cookie      cookie table
req.files       uploaded files table
req.session     platform session table
req.body        raw request body string
```

## `env` fields

```text
env.siteid      current site id
env.sitename    current site name
env.remote_ip   visitor IP
env.is_user     true when the platform session has a user
env.is_admin    true when the session user is the site admin
env.timezone    server timezone
```

`env` also includes the site's variables.

## `ctx` fields and aliases

```text
ctx.req      alias of req
ctx.env      alias of env
ctx.api      alias of api
ctx.http     alias of http
ctx.url      alias of url
ctx.hash     alias of hash
ctx.encoder  alias of encoder
ctx.decoder  alias of decoder
ctx.params   route parameters
ctx.query    query-string table
ctx.body     parsed body after json/form middleware, otherwise the raw body string
```

## Body parsing

`ctx.body` starts as the raw request body. It becomes a table only after one of these middlewares runs:

```lua
app:use("json")   -- parses application/json into ctx.body
app:use("form")   -- parses application/x-www-form-urlencoded into ctx.body
```

If you only need ordinary form fields, `req.post` is already populated by PHP. Use `ctx.body` when you want the parsed value after middleware.

## Route parameters

```lua
app:get("/post/:slug", function(ctx)
    local slug = ctx.params.slug
    return ctx:text(slug)
end)
```

## Example

```lua
app:get("/search", function(ctx)
    local q = ctx.query.q or ""
    local page = tonumber(ctx.query.page) or 1
    return ctx:json({ query = q, page = page })
end)

app:post("/api/items", function(ctx)
    local payload = ctx.body or {}
    return ctx:json({ name = payload.name })
end, { middleware = "json" })
```

## Related

- `responses` — how to answer with JSON, HTML, redirects and errors.
- `middleware` — json/form/auth/csrf behavior.
- `routing` — route params and handlers.
- `twig` — how context data reaches templates.
