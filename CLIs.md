# CLIs

Inventory of command-line tools published across the projects indexed by this marketplace, the channels each ships through today, and the unified pattern they should converge on.

> **CLIs vs Plugins.** This file lists **CLIs** — Go binaries humans (and CI) install on a workstation or build agent. The plugins listed in [README.md](README.md) are a different artifact: Claude Code skill bundles that *wrap* these CLIs so AI agents call them correctly. The two are complementary; each CLI typically has a corresponding plugin.

## Quick install matrix

| CLI | Curl (sh) | PowerShell | Homebrew | WinGet | Scoop | AUR / Snap | Choco | Go | Plugin (Claude Code) | Install skill |
|---|---|---|---|---|---|---|---|---|---|---|
| [`specscore`](https://github.com/synchestra-io/specscore-cli) | ✅ [`specscore.md/install/get-cli`](https://specscore.md/install/get-cli) | ✅ [`.../install/get-cli.ps1`](https://specscore.md/install/get-cli.ps1) | ✅ pending tap repo¹ | ✅ `Synchestra.SpecScore` | ✅ `synchestra-io/specscore` | — | — | ✅ | [`specscore@sneat-co`](https://github.com/synchestra-io/ai-plugin-specscore) | [`specscore:install`](https://github.com/synchestra-io/ai-plugin-specscore/tree/main/skills/install) |
| [`datatug`](https://github.com/datatug/datatug-cli) | ✅ [`datatug.io/install/get-cli`](https://datatug.io/install/get-cli) | ✅ [`.../install/get-cli.ps1`](https://datatug.io/install/get-cli.ps1) | ✅ `datatug/tap` | — | — | — | — | ✅ | [`datatug@sneat-co`](https://github.com/datatug/datatug-ai-skills) | [`datatug:install`](https://github.com/datatug/datatug-ai-skills/tree/main/skills/install) |
| [`ingitdb`](https://github.com/ingitdb/ingitdb-cli) | 🚧 see [gap](#ingitdb--three-checksum-manifests) | 🚧 | ✅ `ingitdb/cli` | ✅ | ✅ `ingitdb/scoop-bucket` | ✅ AUR (`ingitdb-bin`) + Snap | ✅ | ✅ | [`ingitdb@sneat-co`](https://github.com/ingitdb/ingitdb-ai-skills) | [`ingitdb:install`](https://github.com/ingitdb/ingitdb-ai-skills/tree/main/skills/install) |
| [`synchestra`](https://github.com/synchestra-io/synchestra) | 🚧 see [gap](#synchestra--multi-component-releases-repo) | 🚧 | 🚧 pending tap repo¹ | — | — | — | — | ✅ | [`synchestra@sneat-co`](https://github.com/synchestra-io/ai-plugin-synchestra) | [`synchestra:install`](https://github.com/synchestra-io/ai-plugin-synchestra/tree/main/skills/install) |
| [`sneat-go-cli`](https://github.com/sneat-co/sneat-go-cli) | planned | planned | planned | planned | planned | planned | — | planned | _none yet_ | _none yet_ |

Legend: ✅ shipping · 🚧 blocked / pending · — not applicable

¹ The shared Homebrew tap `synchestra-io/homebrew-tap` ships specscore today; synchestra will join it once its release pipeline converges on the unified convention (see [gap](#synchestra--multi-component-releases-repo)).

## Unified install convention

Every CLI in this index should converge on the following shape. The first two CLIs above (`specscore`, `datatug`) already follow it end-to-end; the rest have documented gaps below.

### Artifacts (produced by GoReleaser)

| Artifact | Pattern | Example (`datatug v0.3.0`) |
|---|---|---|
| Archive (Unix) | `{project}_{version}_{os}_{arch}.tar.gz` | `datatug_0.3.0_linux_amd64.tar.gz` |
| Archive (Windows) | `{project}_{version}_{os}_{arch}.zip` | `datatug_0.3.0_windows_amd64.zip` |
| Checksum manifest | `{project}_{version}_checksums.txt` | `datatug_0.3.0_checksums.txt` |
| Binary in archive | `{project}` (`.exe` on Windows) | `datatug` |

### Install URLs (hosted on the project's marketing site)

| URL | Source of truth |
|---|---|
| `https://{domain}/install/get-cli` | `{cli-repo}/scripts/install.sh` |
| `https://{domain}/install/get-cli.ps1` | `{cli-repo}/scripts/install.ps1` |

The marketing site's build pipeline fetches the scripts from the CLI repo at build time (specscore is the canonical example — see `tools/site-generator/build.js` in [synchestra-io/specscore](https://github.com/synchestra-io/specscore)). The CLI repo owns the script; the site is a CDN for it.

### Install script invariants

Across CLIs, the install scripts differ **only** in three values declared at the top of the file:

```sh
REPO="{org}/{cli-repo}"        # e.g. datatug/datatug-cli
BIN_NAME="{project}"           # e.g. datatug
# (and the env-var prefix used in DATATUG_VERSION, DATATUG_INSTALL_DIR)
```

Everything else — OS/arch detection, archive URL construction, checksum verification, install-dir resolution, PATH advisory, shadow-binary cleanup — is byte-identical. New CLIs can be added by copying the template from [`datatug-cli/scripts/install.sh`](https://github.com/datatug/datatug-cli/blob/main/scripts/install.sh) and changing those three values.

### Install directory defaults

| Platform | Default | Override env var |
|---|---|---|
| Linux/macOS (root) | `/usr/local/bin` | `{PROJECT}_INSTALL_DIR` |
| Linux/macOS (non-root, writable `/usr/local/bin`) | `/usr/local/bin` | `{PROJECT}_INSTALL_DIR` |
| Linux/macOS (non-root, default) | `~/.local/bin` | `{PROJECT}_INSTALL_DIR` |
| Windows | `%LOCALAPPDATA%\Programs\{project}\bin` | `{PROJECT}_INSTALL_DIR` |

### Package manager priorities (ROI-ranked)

When adding new install channels to a CLI, prioritize in this order. The list is ordered by audience reach divided by setup-and-maintenance cost:

1. **Curl/PowerShell one-liners** (`{domain}/install/get-cli{,.ps1}`). Highest ROI: works on every Unix and modern Windows out of the box, no third-party account needed. The canonical "first-run experience" for a CLI.
2. **Homebrew** (`brew install {tap}/{name}` or `brew install {name}` for a core formula). Default on macOS, widely used on Linux. GoReleaser publishes via a `brews:` block to a tap repo your org owns.
3. **WinGet** (`winget install {Publisher.Name}`). Ships with Windows 10/11; the modern default on Windows. GoReleaser publishes via a `winget:` block; PRs are auto-opened against `microsoft/winget-pkgs`. Requires a classic PAT with `public_repo` scope.
4. **Scoop** (`scoop install {bucket}/{name}`). Popular among Windows developers. GoReleaser publishes via a `scoops:` block to a bucket repo your org owns.
5. **Go install** (`go install github.com/.../...@latest`). Trivial for any Go-shop. Add a one-line section to README; no release work needed.
6. **AUR / Snap / Chocolatey.** Niche; only ship if your audience explicitly asks. AUR for Arch users, Snap for Ubuntu-ish desktops, Chocolatey for older Windows-admin workflows.

The first two channels (curl + Homebrew) cover ≥90 % of real installs across macOS/Linux/Windows developer workstations. Channels 3–5 are easy wins via GoReleaser blocks. Channels 6+ should be demand-driven.

## Per-CLI gaps and follow-ups

### `ingitdb` — three checksum manifests

ingitdb's release pipeline splits into three parallel jobs (Linux+AUR, macOS+Homebrew+notarization, Windows+WinGet+Scoop+Chocolatey), each producing its own checksum file:

| Job | Config | Checksum file |
|---|---|---|
| Linux | [`.github/goreleaser-linux.yaml`](https://github.com/ingitdb/ingitdb-cli/blob/main/.github/goreleaser-linux.yaml) | `checksums.txt` |
| macOS | [`.github/goreleaser-homebrew.yaml`](https://github.com/ingitdb/ingitdb-cli/blob/main/.github/goreleaser-homebrew.yaml) | `checksums-darwin.txt` |
| Windows | [`.github/goreleaser-windows.yaml`](https://github.com/ingitdb/ingitdb-cli/blob/main/.github/goreleaser-windows.yaml) | `checksums-windows.txt` |

The unified install script expects a single manifest at `{project}_{version}_checksums.txt`. To converge, either:

- **Restructure the release pipeline** to produce a single unified manifest (likely requires reshuffling parallel jobs around macOS notarization).
- **Extend the install-script template** with platform-aware checksum lookup (fall back to `checksums-{os}.txt`). Weakens the "diff is only 3 vars" guarantee.
- **Skip checksum verification.** Strongest unified shape, weakest security. Not recommended.

Until resolved, ingitdb users have brew/AUR/snap/winget/choco/scoop/go — six install channels — so no user is blocked.

### `synchestra` — multi-component releases repo

synchestra CLI binaries are published to a sibling repo, `synchestra-io/synchestra-releases`, which already hosts multi-component releases:

```
synchestra-io/synchestra-releases
├── cli-v0.x.y       ← CLI binaries (planned)
├── servers-v0.x.y   ← Servers binaries (10+ existing releases)
├── hub-v0.x.y       ← future
└── ws-v0.x.y        ← future
```

The `cli-` tag prefix and the cross-repo upload are intentional: they let one releases repo host the whole Synchestra umbrella. The current `synchestra-cli/.goreleaser.yml` sets `release.disable: true` and a follow-up workflow step uploads `dist/*` to the releases repo with tag `cli-${TAG}`.

The unified install script assumes `github.com/{REPO}/releases/download/${TAG}/...` — single repo, plain `v*` tags. For synchestra it needs:

- `RELEASES_REPO` (different from source `REPO`): `synchestra-io/synchestra-releases`
- `RELEASE_TAG_PREFIX`: `cli-`

These can be added as two optional template variables (empty defaults for the other CLIs). Until that template extension lands, the `https://synchestra.io/get-cli` reference in [synchestra/README.md](https://github.com/synchestra-io/synchestra) is aspirational.

The synchestra.io marketing site source lives at [`synchestra-io/synchestra-ws/apps/landing/src/`](https://github.com/synchestra-io/synchestra-ws/tree/main/apps/landing/src) — once the install scripts exist in `synchestra-cli/scripts/`, copying them into `apps/landing/src/install/` is one PR.

### `sneat-go-cli` — not yet built

Currently README-only. When implementation begins, the cleanest path is to start from the unified template directly: GoReleaser with the artifact naming above, `scripts/install.{sh,ps1}` copied from datatug, and a Homebrew tap under `sneat-co`.

## Open questions

- **`synchestra-io/homebrew-tap` repo provisioning.** A shared tap (one repo, one formula per CLI) is the standard pattern (hashicorp/homebrew-tap, goreleaser/homebrew-tap, …). The repo needs to exist with a `main` branch before the next specscore release runs goreleaser. Initial commit ready locally; awaiting `gh repo create`.
- **datatug-io deployment.** The datatug.io site currently has no deployment configuration (`firebase.json`, `vercel.json`, etc.). Once a host is chosen, ensure `/install/get-cli` is served with `Content-Type: text/x-shellscript` (the curl-pipe-to-sh flow works regardless of content-type, but proper headers improve `curl -O` ergonomics and editor previews).
- **Plugin install verification.** Every plugin listed has an `install` skill (`{cli}:install`). Manual smoke-test of each skill against this matrix would catch any drift.

## Maintenance

When a CLI changes its install surface — adds a new channel, migrates a URL, ships a new package manager — update this file. The matrix is the single source-of-truth for "how can I install this?" across the whole ecosystem; out-of-date entries are worse than no entries.
