# Users and auth

Wapka has a platform session. Lua can read the current session user, but ordinary password login is not a Lua session helper.

## Session values

```lua
env.is_user    -- true when the platform session has a user
env.is_admin   -- true when that user is the site admin
api.users.me() -- current platform user profile, or nil
```

## Protect a route

```lua
local app = framework()

app:use("auth")   -- every route now requires env.is_user

app:get("/account", function(ctx)
    local me = api.users.me()
    if not me then
        return ctx:redirect("/")
    end
    return ctx:render("account", { user = me })
end)
```

`app:use("auth")` returns a JSON `401` when the visitor is not signed in. `app:use("admin")` returns `403` unless `env.is_admin` is true.

You can also apply them per route:

```lua
app:post("/account/save", handler, {
    middleware = { "auth", "form", "csrf" }
})
```

## User API

```text
api.users.get(id) -> table|nil
api.users.by_username(username) -> table|nil
api.users.by_email(email) -> table|nil
api.users.list(filter?) -> { data, pagination, meta }
api.users.create(data) -> table|nil
api.users.update(id, data) -> table|nil
api.users.login(username_or_email, password) -> table|nil
api.users.online(filter?) -> { data, pagination, meta }
api.users.me() -> table|nil
api.users.delete(id) -> table|nil
api.users.stats() -> number
```

`api.users.login` checks a username/email and password and returns the user profile. **It does not create a session and does not set `env.is_user`.** Do not write code that claims it logs the user in.

For OAuth connect flows, use:

```text
api.auth.session_create(site_id, payload) -> table|nil
api.auth.session_check(session_id) -> table|nil
```

These create and read the platform connect session; they are not a password-login replacement.

## Password login

If you need a custom password login, you must implement a real session yourself. `api.users.create` can register a user without an existing platform session. After `api.users.login` succeeds, issue your own signed HttpOnly cookie (for example user id + expiry signed with `hash.hmac` and a site secret), verify it on later requests, and never claim it changes `env.is_user`. Prefer the platform/Wapka login flow when possible.

## Related

- `api.users` — exact generated signatures.
- `middleware` — auth/admin gates and CSRF.
- `form` — login/register form structure.
- `rules` — auth mistakes.
