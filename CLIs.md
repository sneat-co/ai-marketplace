# CLIs

Inventory of command-line tools published across the projects indexed by this marketplace, the channels each ships through today, and the unified pattern they should converge on.

> **CLIs vs Plugins.** This file lists **CLIs** — Go binaries humans (and CI) install on a workstation or build agent. The plugins listed in [README.md](README.md) are a different artifact: Claude Code skill bundles that *wrap* these CLIs so AI agents call them correctly. The two are complementary; each CLI typically has a corresponding plugin.
>
> **Scope.** AI-facing CLIs (those with a paired Claude Code plugin) sit at the top of the matrix. **Server/operator CLIs** that share the same install infrastructure (`synchestra-host`, `synchestra-channel`, `synchestra-runner`) are listed below them so all install scripts have a single home — even when there's no AI plugin counterpart.

## Quick install matrix

| CLI | Curl (sh) | PowerShell | Homebrew | WinGet | Scoop | AUR / Snap | Choco | Go | Plugin (Claude Code) | Install skill |
|---|---|---|---|---|---|---|---|---|---|---|
| **[specscore-cli](https://github.com/synchestra-io/specscore-cli)**<br>*[install](https://specscore.md/install)* | ✅ [`get-cli`](https://specscore.md/install/get-cli) | ✅ [`get-cli.ps1`](https://specscore.md/install/get-cli.ps1) | ✅ pending tap repo¹ | ✅ `Synchestra.SpecScore` | ✅ `synchestra-io/specscore` | — | — | ✅ | [`specscore@sneat-co`](https://github.com/synchestra-io/ai-plugin-specscore) | [`specscore:install`](https://github.com/synchestra-io/ai-plugin-specscore/tree/main/skills/install) |
| **[datatug-cli](https://github.com/datatug/datatug-cli)**<br>*[install](https://datatug.io/docs/install/)* | ✅ [`get-cli`](https://datatug.io/install/get-cli) | ✅ [`get-cli.ps1`](https://datatug.io/install/get-cli.ps1) | ✅ `datatug/tap` | — | — | — | — | ✅ | [`datatug@sneat-co`](https://github.com/datatug/datatug-ai-skills) | [`datatug:install`](https://github.com/datatug/datatug-ai-skills/tree/main/skills/install) |
| **[ingitdb-cli](https://github.com/ingitdb/ingitdb-cli)**<br>*[install](https://ingitdb.com/docs)* | 🚧 see [gap](#ingitdb--three-checksum-manifests) | 🚧 | ✅ `ingitdb/cli` | ✅ | ✅ `ingitdb/scoop-bucket` | ✅ AUR (`ingitdb-bin`) + Snap | ✅ | ✅ | [`ingitdb@sneat-co`](https://github.com/ingitdb/ingitdb-ai-skills) | [`ingitdb:install`](https://github.com/ingitdb/ingitdb-ai-skills/tree/main/skills/install) |
| **[synchestra](https://github.com/synchestra-io/synchestra)**<br>*[install](https://synchestra.io/install/)* | ✅ [`get-cli`](https://synchestra.io/install/get-cli) | ✅ [`get-cli.ps1`](https://synchestra.io/install/get-cli.ps1) | 🚧 pending tap repo¹ | — | — | — | — | ✅ | [`synchestra@sneat-co`](https://github.com/synchestra-io/ai-plugin-synchestra) | [`synchestra:install`](https://github.com/synchestra-io/ai-plugin-synchestra/tree/main/skills/install) |
| **[sneat-go-cli](https://github.com/sneat-co/sneat-go-cli)** | planned | planned | planned | planned | planned | planned | — | planned | _none yet_ | _none yet_ |
| **[synchestra-host](https://github.com/synchestra-io/synchestra-servers)** ² | ⚠️ [`get-host`](https://synchestra.io/get-host) (pre-unified) | — | — | — | — | — | — | — | _n/a — server_ | _n/a — server_ |
| **[synchestra-channel](https://github.com/synchestra-io/synchestra-servers)** ² | ⚠️ [`get-channel`](https://synchestra.io/get-channel) (pre-unified) | — | — | — | — | — | — | — | _n/a — server_ | _n/a — server_ |
| **[synchestra-runner](https://github.com/synchestra-io/synchestra-servers)** ² | ⚠️ [`get-runner`](https://synchestra.io/get-runner) (pre-unified) | — | — | — | — | — | — | — | _n/a — server_ | _n/a — server_ |

Legend: ✅ shipping · ⚠️ shipping but not yet on the unified pattern · 🚧 blocked / pending · — not applicable

¹ The shared Homebrew tap [`synchestra-io/homebrew-tap`](https://github.com/synchestra-io/homebrew-tap) is live and will ship specscore on the next release. Synchestra CLI will join once an actual `cli-v*` release is cut (the `brews:` block needs to be added to its `.goreleaser.yml` first).

² Synchestra server CLIs share the [`synchestra-io/synchestra-releases`](https://github.com/synchestra-io/synchestra-releases) artifact host with the synchestra CLI but use the `servers-v*` tag prefix. They predate the unified install-script template and use their own `get-*` scripts at the synchestra.io root. See the [gap](#synchestra-server-cli-migration) below.

## Unified install convention

Every CLI in this index should converge on the following shape. The first three CLIs above (`specscore`, `datatug`, `synchestra`) already follow it end-to-end; the rest have documented gaps below.

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

Across CLIs, the install scripts differ **only** in the top-of-file configuration block. For a single-repo CLI (source and releases in the same repo, plain `v*` tags), only three values matter:

```sh
REPO="{org}/{cli-repo}"        # e.g. datatug/datatug-cli
BIN_NAME="{project}"           # e.g. datatug
# (and the env-var prefix used in DATATUG_VERSION, DATATUG_INSTALL_DIR)
RELEASES_REPO=""               # empty → defaults to $REPO
RELEASE_TAG_PREFIX=""          # empty → no prefix (use /releases/latest)
```

For CLIs whose binaries ship to a multi-component releases repo (e.g. `synchestra-io/synchestra-releases` holds `cli-v*`, `servers-v*`, …), two more knobs come into play — but the script body is unchanged:

```sh
REPO="synchestra-io/synchestra"
BIN_NAME="synchestra"
RELEASES_REPO="synchestra-io/synchestra-releases"   # download from here
RELEASE_TAG_PREFIX="cli-"                           # filter releases by this prefix
```

Everything else — OS/arch detection, archive URL construction, checksum verification, install-dir resolution, PATH advisory, shadow-binary cleanup — is byte-identical. New CLIs can be added by copying the template from [`datatug-cli/scripts/install.sh`](https://github.com/datatug/datatug-cli/blob/main/scripts/install.sh) and changing the config block.

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

### Synchestra server CLI migration

The three server-component CLIs (`synchestra-host`, `synchestra-channel`, `synchestra-runner`) currently install via legacy scripts at the synchestra.io root (`/get-host`, `/get-channel`, `/get-runner`). Each script is hand-rolled, doesn't share structure with the unified template, and lacks the PathAdvisory + shadow-binary handling the unified scripts ship with.

To migrate:

1. Add `scripts/install-{host,channel,runner}.{sh,ps1}` to [`synchestra-io/synchestra-servers`](https://github.com/synchestra-io/synchestra-servers) using the unified template, with these config blocks:
   ```sh
   REPO="synchestra-io/synchestra-servers"
   BIN_NAME="synchestra-{host|channel|runner}"
   RELEASES_REPO="synchestra-io/synchestra-releases"
   RELEASE_TAG_PREFIX="servers-"
   ```
2. Host them at `synchestra.io/install/get-{host,channel,runner}{,.ps1}` (mirror the synchestra-cli setup).
3. Add 301 redirects from `/get-{host,channel,runner}` to the new canonical paths.
4. Update [`synchestra-ws/README.md`](https://github.com/synchestra-io/synchestra-ws) and any docs that reference the old URLs.

This is mechanical work — the template already supports the multi-component releases shape. Until done, the server CLIs use their pre-unified install scripts, which work but lack the polish (PATH advisory, shadow-binary cleanup, PowerShell) of the unified scripts.

### `sneat-go-cli` — not yet built

Currently README-only. When implementation begins, the cleanest path is to start from the unified template directly: GoReleaser with the artifact naming above, `scripts/install.{sh,ps1}` copied from datatug, and a Homebrew tap under `sneat-co`.

## Open questions

- **datatug-io deployment.** The datatug.io site currently has no deployment configuration (`firebase.json`, `vercel.json`, etc.). Once a host is chosen, ensure `/install/get-cli` is served with `Content-Type: text/x-shellscript` (the curl-pipe-to-sh flow works regardless of content-type, but proper headers improve `curl -O` ergonomics and editor previews).
- **specscore `/install/` canonical.** specscore.md uses site-wide `trailingSlash: false`, so `/install` is canonical and `/install/` 301-redirects to it (opposite of datatug.io and synchestra.io, which use `/install/` canonical). Both URLs work everywhere, but the redirect direction differs by site. Unifying specscore to trailing-slash canonical would either require flipping `trailingSlash` globally (affects every page) or restructuring `install.md` → `install/index.md` (site-generator change). Low priority — both URLs already resolve correctly.
- **Plugin install verification.** Every AI-facing CLI has an `install` skill (`{cli}:install`). Manual smoke-test of each skill against this matrix would catch any drift.

## Maintenance

When a CLI changes its install surface — adds a new channel, migrates a URL, ships a new package manager — update this file. The matrix is the single source-of-truth for "how can I install this?" across the whole ecosystem; out-of-date entries are worse than no entries.
