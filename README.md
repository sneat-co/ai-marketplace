# AI Marketplace

Claude Code marketplace for AI plugins published across the projects listed below.

> **Looking for the CLIs themselves?** This marketplace indexes Claude Code **plugins** (skill bundles that teach AI agents how to drive each CLI). The CLIs are separate binaries that humans (and CI) install on a workstation. See [CLIs.md](CLIs.md) for the install matrix, unified install convention, and the canonical `curl … | sh` URLs.
>
> **Indexed CLIs:** [`synchestra`](https://github.com/synchestra-io/synchestra), [`datatug`](https://github.com/datatug/datatug-cli), [`ingitdb`](https://github.com/ingitdb/ingitdb-cli), [`sneat-go-cli`](https://github.com/sneat-co/sneat-go-cli) _(planned)_.
>
> **Looking for SpecScore plugins?** `specscore` and `specstudio` moved to their own dedicated marketplace at [`specscore/ai-marketplace`](https://github.com/specscore/ai-marketplace) on 2026-05-18 — symmetric with the standard's home at [`specscore.md`](https://specscore.md) and SpecScore Studio at [`specscore.studio`](https://specscore.studio).

## Install

Add this marketplace to Claude Code once:

```
/plugin marketplace add sneat-co/ai-marketplace
```

Then install any plugin from it:

```
/plugin install synchestra@sneat-co
/plugin install datatug@sneat-co
```

## Plugins by project

### [Synchestra.io](https://synchestra.io)

| Plugin | Install | Repository |
|---|---|---|
| `synchestra` | `/plugin install synchestra@sneat-co` | [ai-plugin-synchestra](https://github.com/synchestra-io/ai-plugin-synchestra) |

### [SpecScore](https://specscore.md) *(moved to dedicated marketplace)*

The SpecScore-aligned plugins (`specscore`, `specstudio`) moved to [`specscore/ai-marketplace`](https://github.com/specscore/ai-marketplace) on 2026-05-18 — symmetric with [`specscore.md`](https://specscore.md) (the standard) and [`specscore.studio`](https://specscore.studio) (the editor). To install them:

```
/plugin marketplace add specscore/ai-marketplace
/plugin install specscore@specscore
/plugin install specstudio@specscore
```

### [inGitDB](https://ingitdb.com)

| Plugin | Install | Repository |
|---|---|---|
| `ingitdb` | `/plugin install ingitdb@sneat-co` | [ingitdb-ai-skills](https://github.com/ingitdb/ingitdb-ai-skills) |

### [DataTug](https://datatug.io)

| Plugin | Install | Repository |
|---|---|---|
| `datatug` | `/plugin install datatug@sneat-co` | [datatug-ai-skills](https://github.com/datatug/datatug-ai-skills) |

### Planned

| Project | Status | Repository |
|---|---|---|
| ingr-io | planned | [ingr-io](https://github.com/ingr-io) |
| Sneat CLI | planned | [sneat-co/sneat-go-cli](https://github.com/sneat-co/sneat-go-cli) |

## Plugin details

### `synchestra`

Wraps the [`synchestra` CLI](https://github.com/synchestra-io/synchestra) as agent skills, slash commands, and hooks. One skill per CLI command; token-efficient; progressively loaded. The natural install path after the CLI binary itself.

### `ingitdb`

Wraps the [`ingitdb` CLI](https://github.com/ingitdb/ingitdb-cli) as agent skills. Teaches AI agents how to use `ingitdb` for Git-backed database operations: schema validation, record CRUD, materialized views, and serving the database over MCP/HTTP. Distributed from [`ingitdb-ai-skills`](https://github.com/ingitdb/ingitdb-ai-skills).

### `datatug`

Wraps the [`datatug` CLI](https://github.com/datatug/datatug-cli) as agent skills. Teaches AI agents how to use `datatug` for data exploration, dataset management, schema scanning, and query execution. Distributed from [`datatug-ai-skills`](https://github.com/datatug/datatug-ai-skills).

## Why a meta-marketplace

Each plugin lives in its own repository (`ai-plugin-*`). This marketplace is a thin index that lists them, so a single `marketplace add` gives users all published plugins. Plugins retain independent release cadence, independent CI, and independent ownership. See [ADR-0001](https://github.com/synchestra-io/synchestra/blob/main/spec/decisions/0001-extract-ai-plugin.md) in the synchestra repo for the reasoning behind this structure.

## Contributing a plugin

External plugins are not accepted into this marketplace at this time — it is reserved for plugins published by the projects listed above. Third parties are encouraged to publish their own marketplace; Claude Code supports multiple marketplaces per user.

## License

MIT — see [LICENSE](LICENSE).

## Outstanding Questions

- Distribution branding (`marketplace.synchestra.io` CNAME) — deferred pending first external consumer.
- Plugin naming convention across multiple plugins — partially resolved; revisit when a third plugin is added.
