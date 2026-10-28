# Dataset documents

`api.dataset` is a site-scoped JSON document store. Collections are created automatically on first write. There is no schema.

## Signatures

```text
api.dataset.collections() -> string[]
api.dataset.get(id) -> table|nil
api.dataset.find(collection, filter?) -> { data, pagination, meta }
api.dataset.create(collection, data) -> table|nil
api.dataset.update(id, data, replace?) -> table|nil
api.dataset.delete(id, permanent?) -> boolean|nil
api.dataset.restore(id) -> table|nil
api.dataset.purge_collection(collection) -> number|nil
```

All calls return `nil` on an API error. Check before using the result.

## Result shape

```lua
local res = api.dataset.find("posts", {
    filter = { status = "published" },
    order = "id_desc",
    page = 1,
    limit = 20
})

if not res then
    return ctx:error("Could not load posts", 500)
end

for _, doc in ipairs(res.data) do
    local item = doc.data
    -- item.title, item.slug, ...
end

local p = res.pagination
-- p.page, p.limit, p.totalItems, p.totalPages, p.hasNext, p.hasPrev
```

Each item has metadata (`id`, timestamps) and the user document under `item.data`.

## Filter options

```lua
api.dataset.find("products", {
    filter = { status = "active", stock = { gt = 0 } },
    search = "poster",
    order = "price_asc",
    page = 1,
    limit = 20
})
```

- `filter` is a table of conditions.
- `search` is a text search.
- `order` is a sort key such as `id_asc`, `id_desc`, `price_asc`, `updated_desc`.
- `page` is 1-based.
- `limit` defaults to 25 and is capped at 1000.

## Operators

```text
eq (default)  neq  gt  gte  lt  lte
in  nin  regex  like  contains  null  exists
```

Examples:

```lua
filter = { status = { neq = "deleted" } }
filter = { role = { in = { "admin", "editor" } } }
filter = { title = { like = "%lua%" } }
filter = { tags = { contains = "sale" } }
filter = { deleted_at = { null = true } }
filter = { email = { exists = true } }
```

## Create and update

```lua
local saved = api.dataset.create("messages", {
    name = "Ada",
    email = "ada@example.com",
    body = "Hello",
    created_at = os.date("!%Y-%m-%dT%H:%M:%SZ")
})
if not saved then
    return ctx:error("Could not save the message", 500)
end

api.dataset.update(saved.id, {
    status = "read"
})

api.dataset.update(saved.id, {
    status = "archived",
    note = "full replace"
}, true)
```

- `create` requires an authenticated user (`env.is_user`).
- `update` merges by default; pass `replace = true` to replace the whole document.
- `delete` is a soft delete by default; pass `permanent = true` for a hard delete.
- `purge_collection` permanently removes every document in a collection.

## Related

- `api.dataset` — exact generated signatures.
- `auth` — authentication requirements.
- `json_api` — returning dataset results as JSON.
- `rules` — dataset mistakes to avoid.
