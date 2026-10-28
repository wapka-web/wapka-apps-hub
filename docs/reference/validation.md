# Input validation

`validator(data, rules)` returns a validator object. It is pure Lua and does not write the response for you.

## Signature

```lua
local v = validator(data, rules)
```

```text
v:passes() -> boolean
v:fails() -> boolean
v:errors() -> table      -- field -> message
v:first() -> string|nil  -- first error message
v:valid() -> table       -- only fields that have rules
```

## Rules

```text
required
nullable
string
number
email
boolean
table
min:N
max:N
in:a,b,c
regex:pattern
```

- `required` rejects `nil` and empty strings.
- `min:N` / `max:N` check numeric value when the value is numeric, otherwise string length.
- `nullable` skips the other rules when the value is absent.
- A field stops at its first failing rule.
- `valid()` does not deep-clean values; it returns the original values for fields that have rules.

## Example

```lua
app:post("/contact", function(ctx)
    local v = validator(req.post, {
        name = "required|string|min:3|max:80",
        email = "required|email",
        message = "required|string|min:10",
        age = "nullable|number|min:18|max:120"
    })

    if v:fails() then
        ctx:flash("error", v:first())
        return ctx:redirect("/contact")
    end

    local clean = v:valid()
    return ctx:json({ ok = true, name = clean.name })
end)
```

## Related

- `form` — full form flow with CSRF and flash.
- `responses` — returning errors and redirects.
- `rules` — common mistakes.
