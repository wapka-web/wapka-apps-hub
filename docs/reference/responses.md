# Responses, cookies and flash

A handler responds by returning a value or by calling a `ctx:` helper. Returning a helper call is the normal pattern.

## Return values

```lua
return { ok = true }                 -- JSON 200
return "hello"                       -- HTML 200
return 404                           -- status 404, no body
return ctx:json({ ok = true })       -- JSON 200
return ctx:text("hello")             -- text/plain 200
return ctx:html("<h1>Hello</h1>")    -- text/html 200
return ctx:error("Not found", 404)   -- JSON error response
return ctx:redirect("/login")        -- 302 redirect
return ctx:render("home", data)      -- Twig render
```

## Response helpers

```text
ctx:json(data) -> response
ctx:html(content) -> response
ctx:text(content) -> response
ctx:redirect(url, status?) -> response       -- default 302
ctx:status(code) -> ctx                      -- chainable
ctx:error(message, status?) -> response      -- default 400
ctx:render(page_name, data?) -> response
```

`ctx:json`, `ctx:html`, `ctx:text`, `ctx:redirect` and `ctx:render` write the response immediately and return a marker. Return their result from the handler.

## Headers and cookies

```text
ctx:set_header(name, value) -> ctx
ctx:set_cookie(name, value, options?) -> ctx
ctx:get_cookie(name) -> string|nil
ctx:has_cookie(name) -> boolean
ctx:delete_cookie(name, path?) -> ctx
```

Cookie options may include `max_age`, `expires`, `path`, `domain`, `secure`, `http_only`, `same_site`.

## Flash messages

Flash messages survive one redirect:

```lua
ctx:flash("error", "Please fix the form")
return ctx:redirect("/contact")

-- on the next request / render:
local message = ctx:flash("error")   -- reads and consumes it
local all = ctx:flash()              -- reads all and consumes them
ctx:has_flash("error")                -- check without consuming
```

Flash values are not automatically available in Twig. Read them in Lua and pass them to the template.

## Response body helpers

```text
ctx:has_body() -> boolean
ctx:get_body() -> string
ctx:set_body(content) -> ctx
ctx:get_status() -> number
ctx:is_dry_run() -> boolean
```

These are mainly useful in middleware and response hooks.

## Example

```lua
app:post("/contact", function(ctx)
    local name = (req.post.name or ""):match("^%s*(.-)%s*$")
    if name == "" then
        ctx:flash("error", "Name is required")
        return ctx:redirect("/contact")
    end

    ctx:flash("success", "Thanks, " .. name)
    return ctx:redirect("/contact", 303)
end)

app:get("/contact", function(ctx)
    return ctx:render("contact", {
        error = ctx:flash("error"),
        success = ctx:flash("success")
    })
end)
```

## Related

- `context` — request and environment values.
- `middleware` — body parsing and CSRF.
- `twig` — rendering templates.
- `json_api` — JSON endpoint patterns.
