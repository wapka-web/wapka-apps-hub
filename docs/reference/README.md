# Wapka engine reference

Reference-only notes for building Wapka Lua apps. These cards cover the
app-facing Lua and Twig surface, not platform internals.

- Engine: `wapka-monolith@393f2ba`
- Signatures generated: `2026-10-28T04:29:19.097Z`

## Cards

| Card | What it covers |
|---|---|
| [Core lifecycle and source model](core.md) | How init.lua runs, the source file model, sandbox limits, auto-run and raw mode. |
| [Routes and the app object](routing.md) | framework(), app methods, route options, groups, named routes, loadpage and static mounts. |
| [Request, environment and context](context.md) | req fields, env fields, ctx aliases, params, query and body parsing. |
| [Responses, cookies and flash](responses.md) | Handler return values, ctx response helpers, headers, cookies and flash messages. |
| [Middleware and hooks](middleware.md) | Built-in cors/json/form/auth/admin/csrf middleware, route options, hooks and default handler. |
| [Input validation](validation.md) | validator() rules, passes/fails/valid/errors and standard form validation. |
| [Twig templates](twig.md) | Allowed tags, filters and functions, page-name references, autoescape and data passing. |
| [Handle a POST form](form.md) | Render a form, validate input, verify CSRF, flash a message and redirect. |
| [Dataset documents](dataset.md) | Query, filter, paginate, create, update and delete dataset documents. |
| [Users and auth](auth.md) | Current user, protected routes, user records and the difference between login checks and sessions. |
| [JSON API endpoint](json_api.md) | Build a JSON endpoint with route groups, json middleware, validation and proper error responses. |
| [Files and folders](files.md) | Upload, import, list, rename and delete files and folders. |
| [Standard practice and pitfalls](rules.md) | The short checklist that prevents the most common Wapka Lua, Twig and API mistakes. |

## Generated API signatures

- [Lua API signatures](api-signatures.md)
- [Raw signature data](generated.json)

The Markdown signatures are generated from `generated.json`. When the engine
changes, replace both files and re-check the cards.
