# AI Marketplace

Claude Code and Codex marketplace for AI plugins published across the projects listed below.

> **Looking for the CLIs themselves?** This marketplace indexes Claude Code **plugins** (skill bundles that teach AI agents how to drive each CLI). The CLIs are separate binaries that humans (and CI) install on a workstation. See [CLIs.md](CLIs.md) for the install matrix, unified install convention, and the canonical `curl … | sh` URLs.
>
> **Indexed CLIs:** [`wb`](https://github.com/sneat-dev/wb), [`synchestra`](https://github.com/synchestra-io/synchestra), [`datatug`](https://github.com/datatug/datatug-cli), [`ingitdb`](https://github.com/ingitdb/ingitdb-cli), [`sneat-go-cli`](https://github.com/sneat-co/sneat-go-cli) _(planned)_.
>
> **Looking for SpecScore plugins?** `specscore` and `specstudio` moved to their own dedicated marketplace at [`specscore/ai-marketplace`](https://github.com/specscore/ai-marketplace) on 2026-05-18 — symmetric with the standard's home at [`specscore.md`](https://specscore.md) and SpecScore Studio at [`specscore.studio`](https://specscore.studio).

## Install

### Claude Code

Add this marketplace to Claude Code once:

```
/plugin marketplace add sneat-co/ai-marketplace
```

Then install any plugin from it:

```
/plugin install wb@sneat-co
/plugin install synchestra@sneat-co
/plugin install datatug@sneat-co
```

Start a new Claude Code session after installation so the `wb-merge` skill and
`wb-merger` agent load from the installed plugin.

### Codex

Add the same marketplace from a terminal:

```sh
codex plugin marketplace add sneat-co/ai-marketplace
codex plugin add wb@sneat-co
```

Codex reads
`.agents/plugins/marketplace.json`; its entry references `sneat-dev/wb`
directly, so the marketplace never vendors or forks WB's skills and adapters.
Start a new Codex task after installation so the plugin skill is discovered at
session startup.

## Plugins by project

### [WB](https://github.com/sneat-dev/wb)

| Plugin | Claude Code install | Codex | Repository |
|---|---|---|---|
| `wb` | `/plugin install wb@sneat-co` | `codex plugin add wb@sneat-co` | [wb](https://github.com/sneat-dev/wb) |

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
| `datatug` | `/plugin install datatug@sneat-co` | [ai-plugin](https://github.com/datatug/ai-plugin) |

### Planned

| Project | Status | Repository |
|---|---|---|
| ingr-io | planned | [ingr-io](https://github.com/ingr-io) |
| Sneat CLI | planned | [sneat-co/sneat-go-cli](https://github.com/sneat-co/sneat-go-cli) |

## Plugin details

### `wb`

Teaches agents to use WB's deterministic worktree lifecycle and exact-head CI
receipts, including the dedicated `wb-merger` agent and `wb-merge` skill.
Claude Code and Codex load those assets from the released WB repository;
GitHub Copilot consumes the repository's checked-in custom-agent adapter
directly.

### `synchestra`

Wraps the [`synchestra` CLI](https://github.com/synchestra-io/synchestra) as agent skills, slash commands, and hooks. One skill per CLI command; token-efficient; progressively loaded. The natural install path after the CLI binary itself.

### `ingitdb`

Wraps the [`ingitdb` CLI](https://github.com/ingitdb/ingitdb-cli) as agent skills. Teaches AI agents how to use `ingitdb` for Git-backed database operations: schema validation, record CRUD, materialized views, and serving the database over MCP/HTTP. Distributed from [`ingitdb-ai-skills`](https://github.com/ingitdb/ingitdb-ai-skills).

### `datatug`

Wraps the [`datatug` CLI](https://github.com/datatug/datatug-cli) as agent skills for project discovery, schema scans, saved queries, local MCP setup, and the MCP Apps Explorer developer preview. Distributed from [`ai-plugin`](https://github.com/datatug/ai-plugin).

## Why a meta-marketplace

Each plugin lives in its own repository (`ai-plugin-*`). This marketplace is a thin index that lists them, so a single `marketplace add` gives users all published plugins. Plugins retain independent release cadence, independent CI, and independent ownership. See [ADR-0001](https://github.com/synchestra-io/synchestra/blob/main/spec/decisions/0001-extract-ai-plugin.md) in the synchestra repo for the reasoning behind this structure.

## Contributing a plugin

External plugins are not accepted into this marketplace at this time — it is reserved for plugins published by the projects listed above. Third parties are encouraged to publish their own marketplace; Claude Code supports multiple marketplaces per user.

## License

MIT — see [LICENSE](LICENSE).

## Open Questions

- Distribution branding (`marketplace.synchestra.io` CNAME) — deferred pending first external consumer.
- Plugin naming convention across multiple plugins — partially resolved; revisit when a third plugin is added.
