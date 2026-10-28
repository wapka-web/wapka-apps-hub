# Wapka Apps Hub

A curated hub of production-ready Wapka Lua apps. Each app lives in its own
repository, installs through the Wapka web UI, and works immediately with
sensible defaults and ready-to-use content. Most sites only need light content
customization through datasets and Twig.

## Apps

| App | Key | Live preview | Repository |
|---|---|---|---|
| Folio | `folio` | — | `wapka-app-folio` |
| Prism | `prism` | [prism.wapka.website](https://prism.wapka.website/) | `wapka-app-prism` |
| Atlas | `atlas` | — | `wapka-app-atlas` |

Each app has a unique product name and package key. The key is the stable
identifier used by the import list and dataset collections. App source lives in
its own repository; this hub contains the catalogue, shared development
reference and quality rules.

App repositories and download links are being prepared. Live previews are added
as each app is published.

## Quick start

1. Open the app repository.
2. Download the app ZIP from the repository.
3. Import the ZIP through the Wapka web UI.
4. Review the import summary and apply it.
5. Replace or extend the content through the Dataset UI or API.

Site owners do not need CLI or server access. Platform maintainers use internal
tooling for development and testing; that tooling is not part of the public
install flow.

## Hub layout

```text
wapka-apps-hub/
├── README.md          app catalogue and live previews
├── apps.json          machine-readable app index
├── CONTRIBUTING.md    quality, naming and release rules
└── docs/reference/    engine and API reference
```

## App package layout

Every app repository uses the same source layout at the repository root:

```text
wapka-app-<key>/
├── app.json          package metadata and page directories
├── init.lua          app entry script
├── README.md         app-specific documentation
├── smoke.json        optional smoke checks
├── logic/            Lua page logic
│   └── <page>.lua
├── views/            Twig-rendered page content
│   └── <page>.twig
└── data/             optional dataset seeds
    └── <collection>.jsonl
```

`app.json` declares the page directories explicitly:

```json
"pages": {
  "logic": "logic",
  "views": "views"
}
```

`logic/` and `views/` are the standard page directories. Page files pair by
basename: `logic/home.lua` and `views/home.twig` belong to the page `home`.
Templates reference page names, not storage paths: `{% extends "layout" %}`.

## Included content

Each app ships `data/*.jsonl` seed files so it works immediately after import.
Replace or extend the content through the Dataset UI or API. The web
import/seeding flow is still being finalized.

## Quality

Only production-ready apps are listed here. Every app must have a working live
install, passing smoke tests, a responsive layout, complete documentation and
no secrets or unnecessary external dependencies. The full checklist is in
[CONTRIBUTING.md](CONTRIBUTING.md).

## Future: `installer.lua`

Wapka is planning a site-level `installer.lua` hook. It would run during
install, upgrade or uninstall, with an authenticated installer context, and
could use APIs such as `api.sites.current()`, `api.sites.list(page?)` and
`api.dataset.*` for site-specific setup and migrations.

`installer.lua` is **not supported yet**. Current packages must not ship one;
the existing installer only uses `app.json`, page files, dataset seeds and the
optional smoke file.

## Visual editor and app packages

The Wapka Visual editor imports and exports live-site bundles automatically.
Those bundles use the same `app.json`, `init.lua`, `logic/` and `views/` source
layout, plus generated `visual.json` and `BUNDLE.txt` metadata. An app repository
is the authoring form: it can also contain `data/`, `smoke.json` and the app
README.

Generated bundle files should not be copied into app packages.

## Documentation

Development reference material lives in [`docs/reference/`](docs/reference/README.md):

- [Engine reference index](docs/reference/README.md)
- [Lua API signatures](docs/reference/api-signatures.md)
- [Raw signature data](docs/reference/generated.json)
- [Core lifecycle](docs/reference/core.md)
- [Routing](docs/reference/routing.md)
- [Context](docs/reference/context.md)
- [Responses](docs/reference/responses.md)
- [Dataset](docs/reference/dataset.md)
- [Twig](docs/reference/twig.md)
- [Rules and pitfalls](docs/reference/rules.md)

## Status

The hub is being split from the original monorepo. The first stable apps are
Folio, Prism and Atlas. App repositories and download links will be added here
as they are published.

Maintained by [Jonayed Hossan Gazi](https://github.com/jonayed-hossan-gazi) for Wapka — [mail@wapka.org](mailto:mail@wapka.org).
