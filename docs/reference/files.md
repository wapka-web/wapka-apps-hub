# Files and folders

Files are managed through `api.files` and `api.folders`.

## Signatures

```text
api.files.get(id) -> table|nil
api.files.list(filter?) -> { data, pagination, meta }
api.files.upload(data) -> table|nil
api.files.import(url, folder_id, options?) -> table|nil
api.files.import_key(key, folder_id) -> table|nil
api.files.rename(id, name) -> table|nil
api.files.delete(id) -> boolean|nil

api.folders.get(id) -> table|nil
api.folders.list(parent?, sort?) -> table[]
api.folders.create(name, parent?) -> table|nil
api.folders.rename(id, name) -> table|nil
api.folders.delete(id) -> boolean|nil
```

## Upload

A browser upload must be a `multipart/form-data` form with a file field named `file`. PHP exposes it in `req.files`.

```lua
app:post("/upload", function(ctx)
    if not env.is_user then
        return ctx:error("Sign in first", 401)
    end

    local saved = api.files.upload({ folderid = 0 })
    if not saved then
        return ctx:error("Upload failed", 400)
    end

    return ctx:json(saved)
end)
```

```twig
<form method="post" action="/upload" enctype="multipart/form-data">
    <input type="hidden" name="csrf_token" value="{{ csrf_token }}">
    <input type="file" name="file">
    <button type="submit">Upload</button>
</form>
```

The form must also include a valid CSRF token when the `csrf` middleware is enabled.

## Import from a URL

```lua
local saved = api.files.import("https://example.com/image.png", 0, {
    name = "image.png",
    overwrite = true,
    validate = true
})
```

Options: `name`, `perm`, `meta`, `overwrite`, `headers`, `validate`.

## List and folders

```lua
local res = api.files.list({ folderid = 0, page = 1, limit = 20 })
if res then
    for _, file in ipairs(res.data) do
        -- file.name, file.size, ...
    end
end

local folders = api.folders.list(0) or {}
```

`api.files.list` is paginated. `api.folders.list` returns a plain array.

## Related

- `api.files` and `api.folders` — exact generated signatures.
- `form` — multipart form and CSRF.
- `auth` — requiring a signed-in user.
- `rules` — common file mistakes.
