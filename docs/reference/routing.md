# Routes and the app object

`framework()` returns the application object. Register routes on it, then let the engine auto-run it.

## Signatures

```text
framework() -> app

app:get(path, handler, opts?) -> route
app:post(path, handler, opts?) -> route
app:put(path, handler, opts?) -> route
app:patch(path, handler, opts?) -> route
app:delete(path, handler, opts?) -> route
app:head(path, handler, opts?) -> route
app:any(path, handler, opts?) -> route

app:group(prefix, fn) -> nil
app:url(name, params?) -> string|nil
app:use(middleware) -> nil
app:on(event, fn) -> nil
app:set_default(fn) -> nil
app:set_template(name, code) -> nil
app:mount(prefix, handler_or_nil, zip_key?) -> nil
app:auto_run(enabled) -> nil
app:run() -> nil

loadpage(page_name) -> ok, err
```

## Handlers

A handler is either a function or a page name:

```lua
app:get("/", function(ctx)
    return ctx:render("home", { title = "Home" })
end)

app:get("/about", "about")   -- runs logic/about.lua, with ctx available
```

A page-name handler calls `loadpage(page)` and exposes `ctx` inside the page logic.

## Route options

```lua
app:post("/api/items", handler, {
    name = "items.create",
    middleware = "json",          -- string, function, or table of middlewares
    csrf = false                  -- exempt this route from CSRF
})
```

- `name` makes the route available to `app:url("items.create", { id = 7 })`.
- `middleware` can be one name, one function, or a table of names/functions.
- `csrf = false` is for webhooks and similar machine-to-machine endpoints only.

## Groups

```lua
app:group("/api/v1", function()
    app:get("/status", function(ctx)
        return ctx:json({ ok = true })
    end)
end)
```

## Hooks

```lua
app:on("before", function(ctx) end)
app:on("after", function(ctx, result) end)
app:on("error", function(err, ctx) end)
app:on("response", function(ctx) end)
```

`app:set_default(fn)` runs when no route wrote a response.

## Static mounts

The real signature has three arguments:

```lua
app:mount(prefix, handler_or_nil, zip_key)
```

Pass `nil` as the handler to serve a ZIP bundle directly:

```lua
app:mount("/assets", nil, "theme.zip")
```

Do not use the two-argument form from older docs.

## 404 and 405

The framework responds automatically:

- A path with no matching route returns `404` JSON.
- A path that matches another method returns `405` JSON.

You can still return your own `ctx:error(...)` inside a handler.

## Minimal example

```lua
local app = framework()

app:get("/", function(ctx)
    return ctx:render("home", { title = "Home" })
end)

app:get("/about", "about")

app:post("/contact", function(ctx)
    return ctx:json({ received = true })
end, { middleware = "json" })
```

## Related

- `context` — what `ctx` contains.
- `responses` — what a handler should return.
- `middleware` — how route middleware works.
- `core` — lifecycle and auto-run.
