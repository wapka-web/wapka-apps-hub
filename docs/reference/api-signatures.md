# Lua API signatures

Engine: `wapka-monolith@393f2ba`

Generated: `2026-10-28T04:29:19.097Z`

API calls return `nil` on error. Check the result before indexing.

## `api.access`

Access methods, limits, fields, IP lists and auth settings.

> API calls return nil on error. Check before using the result.

- `api.access.overview()` -> `table|nil`
- `api.access.methods.get()` -> `table|nil`
- `api.access.methods.set(disabled, auth_required)` -> `table|nil`
- `api.access.limits.get()` -> `table|nil`
- `api.access.limits.set(public_per_min, user_per_min, admin_per_min)` -> `table|nil`
- `api.access.fields.get()` -> `table|nil`
- `api.access.fields.set(rules)` -> `table|nil`
- `api.access.ips.get()` -> `table|nil`
- `api.access.ips.set(allowlist, blocklist)` -> `table|nil`
- `api.access.auth.get()` -> `table|nil`
- `api.access.auth.set(enable_jwt, guest_user_id, guest_level)` -> `table|nil`

## `api.auth`

OAuth connect sessions.

> API calls return nil on error. Check before using the result.

- `api.auth.session_create(site_id, payload)` -> `table|nil` — OAuth connect session. Payload: return_link, brand_name, providers[].
- `api.auth.session_check(session_id)` -> `table|nil` — Reads an OAuth connect session. Returns status: pending, expired, completed or not_found.

## `api.codes`

Page code blocks and ordering.

> API calls return nil on error. Check before using the result.

- `api.codes.get(id)` -> `table|nil`
- `api.codes.by_resource_id(resource_id)` -> `table|nil`
- `api.codes.list(page_id, page?, limit?)` -> `{ data, pagination, meta }`
- `api.codes.create(page_id, data)` -> `table|nil`
- `api.codes.update(id, data)` -> `table|nil`
- `api.codes.delete(id)` -> `boolean|nil` — Deletes one code block. Requires delete permission.
- `api.codes.copy(code_id, page_id)` -> `table|nil`
- `api.codes.move(code_id, page_id)` -> `table|nil`
- `api.codes.up(code_id)` -> `boolean|nil`
- `api.codes.down(code_id)` -> `boolean|nil`

## `api.dataset`

Site-scoped JSON document store (schemaless NoSQL).

> API calls return nil on error. Check before using the result.

- `api.dataset.collections()` -> `string[]`
- `api.dataset.get(id)` -> `table|nil`
- `api.dataset.find(collection, filter?)` -> `{ data, pagination, meta }` — Returns { data, pagination, meta }. Each item is a document; the user fields are under item.data. Returns nil on API error.
- `api.dataset.create(collection, data)` -> `table|nil` — Requires an authenticated user (env.is_user). Returns the created document or nil.
- `api.dataset.update(id, data, replace?)` -> `table|nil` — Merge by default; pass replace=true for a full replace. Returns the document or nil.
- `api.dataset.delete(id, permanent?)` -> `boolean|nil` — Soft delete by default; pass permanent=true for a hard delete.
- `api.dataset.restore(id)` -> `table|nil`
- `api.dataset.purge_collection(collection)` -> `number|nil`

## `api.files`

Files, uploads, imports and file metadata.

> API calls return nil on error. Check before using the result.

- `api.files.get(id)` -> `table|nil`
- `api.files.list(filter?)` -> `{ data, pagination, meta }`
- `api.files.upload(data)` -> `table|nil` — Uses the multipart upload field named file. Pass { folderid = n } when needed.
- `api.files.import(url, folder_id, options?)` -> `table|nil` — Imports from a URL. Options: name, perm, meta, overwrite, headers, validate.
- `api.files.import_key(key, folder_id)` -> `table|nil`
- `api.files.rename(id, name)` -> `table|nil`
- `api.files.delete(id)` -> `boolean|nil`

## `api.folders`

File folders.

> API calls return nil on error. Check before using the result.

- `api.folders.get(id)` -> `table|nil`
- `api.folders.list(parent?, sort?)` -> `table[]`
- `api.folders.create(name, parent?)` -> `table|nil`
- `api.folders.rename(id, name)` -> `table|nil`
- `api.folders.delete(id)` -> `boolean|nil`

## `api.forums`

Forum categories and forum CRUD.

> API calls return nil on error. Check before using the result.

- `api.forums.get(id)` -> `table|nil`
- `api.forums.list(filter?)` -> `{ data, pagination, meta }`
- `api.forums.create(name, parent?)` -> `table|nil`
- `api.forums.rename(id, name)` -> `table|nil`
- `api.forums.delete(id)` -> `boolean|nil` — Deletes the forum. Requires delete permission.

## `api.messages`

Private messages, conversations and unread counts.

> API calls return nil on error. Check before using the result.

- `api.messages.get(id)` -> `table|nil`
- `api.messages.send(to, message)` -> `table|nil` — Accepts a numeric user id or a username as the first argument.
- `api.messages.chat(user_id, page, limit?, sort?)` -> `{ data, pagination, meta }`
- `api.messages.conversations()` -> `table[]`
- `api.messages.unread()` -> `number`
- `api.messages.edit(id, message)` -> `table|nil`
- `api.messages.delete(id)` -> `boolean|nil` — Deletes one message for the current user.

## `api.pages`

Site pages.

> API calls return nil on error. Check before using the result.

- `api.pages.get(id)` -> `table|nil`
- `api.pages.list(filter?)` -> `{ data, pagination, meta }`

## `api.posts`

Posts inside forums.

> API calls return nil on error. Check before using the result.

- `api.posts.get(id)` -> `table|nil`
- `api.posts.list(filter?)` -> `{ data, pagination, meta }`
- `api.posts.create(data)` -> `table|nil`
- `api.posts.update(id, data)` -> `table|nil`
- `api.posts.delete(id, hard?)` -> `boolean|nil` — Soft-deletes by default; pass hard=true for a permanent delete.
- `api.posts.restore(id)` -> `table|nil`

## `api.sites`

Site lookup and the current site.

> API calls return nil on error. Check before using the result.

- `api.sites.get(id)` -> `table|nil`
- `api.sites.list(page?)` -> `{ data, pagination, meta }`
- `api.sites.current()` -> `table|nil`

## `api.users`

User accounts, profiles, login, online visitors and stats.

> API calls return nil on error. Check before using the result.

- `api.users.get(id)` -> `table|nil`
- `api.users.by_username(username)` -> `table|nil`
- `api.users.by_email(email)` -> `table|nil`
- `api.users.list(filter?)` -> `{ data, pagination, meta }`
- `api.users.create(data)` -> `table|nil`
- `api.users.update(id, data)` -> `table|nil`
- `api.users.login(username_or_email, password)` -> `table|nil` — Validates a username/email + password and returns the user profile, or nil. It does NOT create a session.
- `api.users.online(filter?)` -> `{ data, pagination, meta }`
- `api.users.me()` -> `table|nil` — Returns the profile for the current platform session, or nil.
- `api.users.delete(id)` -> `table|nil` — Soft-deletes by setting type=4.
- `api.users.stats()` -> `number`

## `api.variables`

Site variables.

> API calls return nil on error. Check before using the result.

- `api.variables.list()` -> `table|nil`
- `api.variables.set(key, value)` -> `table|nil`
- `api.variables.remove(key)` -> `boolean|nil` — Removes a site variable. Requires an authenticated user.

## `decoder`

JSON, base64, base36, rot13 and hex decoding.

- `decoder.json(text)` -> `any`
- `decoder.base64(text)` -> `string`
- `decoder.base36(text)` -> `string`
- `decoder.rot13(text)` -> `string`
- `decoder.hex(text)` -> `string`

## `encoder`

JSON, base64, base36, rot13 and hex encoding.

- `encoder.json(data)` -> `string`
- `encoder.base64(data)` -> `string`
- `encoder.base36(data)` -> `string`
- `encoder.rot13(data)` -> `string`
- `encoder.hex(data)` -> `string`

## `hash`

Hashes, HMAC, random bytes and UUIDs.

- `hash.md5(data)` -> `string`
- `hash.sha1(data)` -> `string`
- `hash.sha256(data)` -> `string`
- `hash.sha512(data)` -> `string`
- `hash.crc32(data)` -> `number`
- `hash.hmac(key, data)` -> `string`
- `hash.random(bytes?)` -> `string`
- `hash.equals(a, b)` -> `boolean`
- `hash.uuid()` -> `string`

## `http`

Outbound HTTP client with timeouts, redirects and SSRF protection.

> Options: headers, body, json, form, timeout (max 30), ssl_verify, follow_redirects, max_redirects, max_size.

- `http.get(url, options?)` -> `{ status, body, headers, ok, error }`
- `http.head(url, options?)` -> `{ status, body, headers, ok, error }`
- `http.post(url, options?)` -> `{ status, body, headers, ok, error }`
- `http.put(url, options?)` -> `{ status, body, headers, ok, error }`
- `http.patch(url, options?)` -> `{ status, body, headers, ok, error }`
- `http.delete(url, options?)` -> `{ status, body, headers, ok, error }`
- `http.request(method, url, options?)` -> `{ status, body, headers, ok, error }`

## `log`

Structured site logs.

- `log.info(message)` -> `boolean`
- `log.error(message)` -> `boolean`
- `log.warn(message)` -> `boolean`
- `log.debug(message)` -> `boolean`
- `log.should_debug()` -> `boolean`

## `str`

UTF-8 string helpers.

- `str.split(text, separator)` -> `table`
- `str.trim(text)` -> `string`
- `str.contains(text, needle)` -> `boolean`
- `str.starts_with(text, prefix)` -> `boolean`
- `str.ends_with(text, suffix)` -> `boolean`
- `str.limit(text, length, ending?)` -> `string`
- `str.replace(text, search, replacement)` -> `string`
- `str.lcfirst(text)` -> `string`
- `str.ucfirst(text)` -> `string`
- `str.ucwords(text)` -> `string`

## `url`

URL encode/decode, parse/build and slugs.

- `url.encode(value)` -> `string`
- `url.decode(value)` -> `string`
- `url.rawencode(value)` -> `string`
- `url.rawdecode(value)` -> `string`
- `url.parse(url)` -> `table`
- `url.build(parts)` -> `string`
- `url.slug(text)` -> `string`
- `url.redirect(url)` -> `string` — Sets a 302 redirect and returns an empty string. Prefer ctx:redirect() inside handlers.
