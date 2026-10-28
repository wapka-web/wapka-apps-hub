# JSON API endpoint

Use route groups and the `json` middleware for API endpoints.

## Basic endpoint

```lua
local app = framework()

app:group("/api/v1", function()
    app:get("/status", function(ctx)
        return ctx:json({ ok = true })
    end)

    app:post("/items", function(ctx)
        local body = ctx.body or {}
        local v = validator(body, {
            name = "required|string|min:1|max:120"
        })

        if v:fails() then
            return ctx:error(v:first(), 422)
        end

        return ctx:status(201):json({ name = v:valid().name })
    end, { middleware = "json" })
end)
```

## Rules

- Use `middleware = "json"` on routes that read JSON bodies.
- `ctx.body` is the parsed JSON table after the `json` middleware.
- Validate before writing.
- Return `ctx:json(data)` for success.
- Return `ctx:error(message, status)` for expected errors.
- The framework returns `404` for unknown paths and `405` for a known path with the wrong method.
- Add `app:use("cors")` only when the API must be called from another origin.

## List endpoint with pagination

```lua
app:get("/items", function(ctx)
    local page = tonumber(ctx.query.page) or 1
    local limit = tonumber(ctx.query.limit) or 20

    local res = api.dataset.find("items", {
        order = "id_desc",
        page = page,
        limit = limit
    })

    if not res then
        return ctx:error("Could not load items", 500)
    end

    return ctx:json({
        data = res.data,
        pagination = res.pagination
    })
end)
```

## Related

- `routing` — route groups and options.
- `context` — query and body values.
- `responses` — JSON, errors and status codes.
- `dataset` — paginated data access.
