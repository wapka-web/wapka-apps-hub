# Core lifecycle and source model

Wapka runs Lua 5.1 inside the LuaSandbox extension. Each request starts from the site's `lua_init` script and ends with one HTTP response.

## Source files

- `init.lua` — the entry script. Stored in `site.config.lua_init`.
- `logic/<page>.lua` — page logic, stored as `code.type = 29`.
- `views/<page>.<ext>` — page content, stored as `code.type = 0`.

`views/` is one flat directory. The view extension follows the Wapka page type:

| Page type | Wapka type | Default view |
|---|---|---|
| 0 | HTML | `views/<page>.twig` |
| 1 | Text | `views/<page>.txt` |
| 2 | CSS | `views/<page>.css` |
| 3 | JavaScript | `views/<page>.js` |
| 4 | JSON | `views/<page>.json` |
| 5 | XML | `views/<page>.xml` |

A page name can contain its own extension (`robots.txt`, `sitemap.xml`, `style.css`). The page name is what Lua and Twig use; storage paths are only for the editor.

## How a request runs

```text
request
  → load site lua_init
  → inject globals: req, env, api, http, url, hash, str, log, encoder, decoder,
                    validator, csrf_token, loadpage, framework, print, dump
  → run init.lua
  → framework app auto-runs if one was created and auto_run is still true
  → build response
```

`init.lua` normally starts with:

```lua
local app = framework()
```

You do not need `app:run()`; the engine auto-runs the app after `init.lua` finishes. Call `app:auto_run(false)` only when you want to run it yourself.

## Framework and raw mode

When a framework app exists, it handles routing, middleware, hooks and the response.

If `init.lua` never creates an app, the script runs in raw mode. In raw mode, `print()` and `dump()` output is appended to the response body. Do not mix raw output with a framework app unless you know exactly what you are doing.

## Rendering pages

`ctx:render(name, data)` first looks for a database page named `name`. If the page exists, it renders that page as Twig. If it does not, it falls back to an inline template registered with `app:set_template(name, code)`.

This means:

- The first argument is a page name, not `views/home.twig`.
- Twig `{% extends "layout" %}` and `{% include "card" %}` also use page names.
- All view files are Twig-rendered, including CSS, JS, text, JSON and XML views.
- Twig autoescape is chosen from the top-level page type.

## Sandbox limits

- 10 MB memory per request.
- 10 seconds CPU per request.
- Up to 5 MB of source per code block.
- No SQL, filesystem, shell, process or arbitrary PHP access.
- API access goes through the `api.*` wrappers.

## API errors and list results

`api.*` calls catch engine errors and return `nil`. Always check before indexing:

```lua
local res = api.dataset.find("posts", { limit = 20 })
if not res then
    return ctx:error("Could not load posts", 500)
end
```

Paginated list methods return:

```lua
{
    data = { ... },          -- the actual rows/documents
    pagination = {
        page = 1,
        limit = 20,
        totalItems = 0,
        totalPages = 1,
        hasNext = false,
        hasPrev = false
    },
    meta = { ... }
}
```

Some helpers return a plain array instead: `api.folders.list` and
`api.messages.conversations`.

## Related

- `routing` — how to register routes and handlers.
- `context` — the `req`, `env` and `ctx` objects.
- `responses` — how return values become HTTP responses.
- `rules` — the short checklist of common mistakes.
