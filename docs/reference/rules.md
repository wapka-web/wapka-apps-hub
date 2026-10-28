# Standard practice and pitfalls

Use this as the final check before writing or reviewing Lua/Twig code.

## Source and lifecycle

1. Use `init.lua`, `logic/<page>.lua` and `views/<page>.<ext>`. Do not invent folders.
2. `init.lua` starts with `local app = framework()`. The engine auto-runs the app; do not call `app:run()` unless you disabled auto-run.
3. Use page names in Lua and Twig references: `ctx:render("home")`, `{% extends "layout" %}`. Never use `views/home.twig` or `logic/home.lua` there.
4. `init.lua` cannot be deleted or renamed.

## Request and response

5. `ctx.body` is the raw body string until the `json` or `form` middleware parses it. `req.post` is already available for ordinary form posts.
6. A handler returns a table for JSON, a string for HTML, a number for a status, or the result of `ctx:json/html/text/redirect/render`.
7. `ctx:render` tries a database page with that name first, then an inline template.
8. Use `ctx:error(message, status)` for expected errors, and `ctx:redirect` after a form POST.

## API

9. `api.*` returns `nil` on an API error. Check before indexing.
10. Paginated list APIs return `{ data, pagination, meta }`. Iterate `res.data`, not `res.items`. `api.folders.list` and `api.messages.conversations` return plain arrays.
11. Dataset writes require an authenticated user (`env.is_user`).
12. `api.dataset.update` merges by default; pass `replace = true` for a full replace.
13. `api.dataset.delete` is soft by default; pass `permanent = true` for a hard delete.
14. `api.users.login` only validates credentials and returns a profile. It does not create a session or set `env.is_user`.

## Twig

15. Twig is sandboxed. Use only the allowed tags, filters and functions. No PHP functions, object methods or filesystem access.
16. Twig has no implicit `csrf_token`, `flash`, `site` or `user` variables. Pass them from Lua as template data.
17. Use `|raw` only for trusted HTML. Normal output is autoescaped.
18. Autoescape follows the page type: HTML/Text/JSON/XML use `html`; CSS uses `css`; JS uses `js`.

## Security and forms

19. Browser POST forms need the CSRF middleware and a hidden `csrf_token` field.
20. Validate input with `validator()` before writing.
21. Never use SQL, shell commands, filesystem access or arbitrary PHP from Lua.
22. `app:mount` is `app:mount(prefix, handler_or_nil, zip_key)`. Older two-argument examples are wrong.

## Related

- `core` — lifecycle and source model.
- `twig` — exact Twig surface.
- `dataset` — data access.
- `auth` — session and user behavior.
