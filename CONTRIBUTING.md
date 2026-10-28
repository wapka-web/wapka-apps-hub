# Contributing to Wapka Apps Hub

The hub lists production-ready Wapka Lua apps. Each app is maintained in its own
repository under `wapka-web`.

## App repository naming

Use `wapka-app-<key>`:

- `wapka-app-folio`
- `wapka-app-prism`
- `wapka-app-atlas`

The app package sits at the repository root. Do not wrap it in a `lua-apps/`
directory.

## Required app files

```text
app.json
init.lua
README.md
smoke.json
logic/
views/
data/
CHANGELOG.md
LICENSE
```

## Quality gate

An app is listed in the hub only when it has:

- a working live install on a real Wapka site
- passing package validation
- passing smoke tests
- a responsive layout checked on mobile and desktop
- complete README with routes, data model and install notes
- no secrets, private endpoints or unnecessary external dependencies
- working empty states and error pages
- no `_framework` calls, direct SQL or filesystem access
- a clear license and changelog

Only `stable` apps are listed in the README and `apps.json`. Experimental work
stays out of the hub until it passes the checklist.

## Hub entry

When an app repository is published:

1. Add its GitHub archive link to `apps.json`:

   ```text
   https://github.com/wapka-web/wapka-app-<key>/archive/refs/heads/main.zip
   ```

2. Add the live preview URL if the app is deployed.
3. Add the app to the README table.
4. Keep the `key`, repository name and app `app.json` key consistent.

## Versioning

- Tag releases as `v<major>.<minor>.<patch>`.
- Add release notes to `CHANGELOG.md`.
- Keep `app.json` version aligned with the latest release.

## Packaging

Keep the package root flat:

```text
app.json
init.lua
logic/
views/
data/
smoke.json
README.md
```

The Wapka importer will normalize a single top-level folder in downloaded
archives. That importer work is planned; until it lands, verify the current
import path handles the GitHub archive wrapper.

## Security

- Never commit secrets, tokens or private credentials.
- Do not add direct SQL, shell commands, filesystem access or arbitrary PHP.
- Keep writes behind the platform auth/session bridge.
- Use Twig autoescaping and only pass trusted HTML through `|raw`.

## License

Every app repository must include a `LICENSE`. The license choice is pending for
this collection; do not publish an app without one.
