# getfledge

Landing page for [Fledge](https://github.com/CorvidLabs/fledge) — one CLI for your entire dev lifecycle.

**Live:** [corvidlabs.github.io/getfledge](https://corvidlabs.github.io/getfledge/)
**Docs:** [corvidlabs.github.io/fledge](https://corvidlabs.github.io/fledge/)

## What is Fledge?

Fledge is a single Rust binary that replaces your Makefile, shell scripts, and scattered tooling. Scaffold projects, run tasks, review code with AI, manage releases — all from one CLI with one config file.

- **Humans** get interactive prompts, colored output, and pretty diffs
- **AI agents** get structured JSON — no parsing hacks needed
- **New hires** get guided setup and zero tribal knowledge

## Where the canonical docs live

This repo is just the marketing landing page. The real reference material is in the [main fledge repo's mdbook](https://corvidlabs.github.io/fledge/):

| Topic | Page |
|-------|------|
| Getting started | [Quick Start](https://corvidlabs.github.io/fledge/getting-started/quick-start.html) |
| Tasks and lanes | [Run: Tasks and Lanes](https://corvidlabs.github.io/fledge/lanes.html) |
| **`fledge.toml` full schema** | [`fledge.toml` Reference](https://corvidlabs.github.io/fledge/fledge-toml.html) |
| **`plugin.toml` reference** | [Extend: Plugins](https://corvidlabs.github.io/fledge/plugins.html) |
| Plugin wire protocol | [Plugin Protocol (fledge-v1)](https://corvidlabs.github.io/fledge/plugin-protocol.html) |
| Global config | [Configuration](https://corvidlabs.github.io/fledge/configuration.html) |
| Every CLI flag | [CLI Reference](https://corvidlabs.github.io/fledge/cli-reference.html) |

## Config files at a glance

> Quick orientation only — see the linked pages above for the full schema.

### `fledge.toml` (per project)

```toml
[tasks]
build = "cargo build"

[tasks.test]
cmd = "cargo test"
description = "Run the test suite"
deps = ["build"]
env = { RUST_LOG = "debug" }
dir = "."

[lanes.ci]
description = "Full CI pipeline"
steps = [
  { parallel = ["fmt", "lint"] },
  "test",
  "build",
]
fail_fast = true

[release]
files = ["flake.nix"]
```

Sections: `[tasks]` (short or full form) · `[lanes.<name>]` with `steps` (task ref / `{ run = "..." }` / `{ parallel = [...] }`) · `[release].files` for extra version-bump targets.

→ [Full reference](https://corvidlabs.github.io/fledge/fledge-toml.html)

### `plugin.toml` (per plugin repo)

```toml
[plugin]
name = "fledge-deploy"
version = "0.1.0"
description = "Deploy to a target environment"
author = "You"
protocol = "fledge-v1"          # opt into the structured plugin protocol

[capabilities]                  # all default to false
exec = true                     # run shell commands via the protocol
store = true                    # persist key/value state
metadata = false                # read parsed fledge.toml, git status, etc.

[[commands]]
name = "deploy"
description = "Deploy the project"
binary = "bin/fledge-deploy"

[hooks]                         # all optional
build = "cargo build --release"
post_install = "scripts/post-install.sh"
```

Hooks: `build`, `post_install`, `post_remove`, `pre_init`, `post_work_start`, `pre_push`.

→ [Full reference](https://corvidlabs.github.io/fledge/plugins.html#plugintoml-reference)
→ [Wire protocol](https://corvidlabs.github.io/fledge/plugin-protocol.html)

### Global config (`~/.config/fledge/config.toml`)

```toml
[defaults]
author = "Your Name"
github_org = "YourOrg"
license = "MIT"

[ai]
provider = "claude"             # or "ollama"

[ai.claude]
model = "sonnet"

[github]
token = "ghp_..."
```

→ [Full reference](https://corvidlabs.github.io/fledge/configuration.html)

## Stack

- Static HTML + CSS (no framework, no build step)
- GitHub Pages deployment
- Inter + JetBrains Mono fonts
- IntersectionObserver scroll animations

## Local development

This repo eats its own dog food — tasks are defined in `fledge.toml`.

```bash
fledge run serve            # python3 http.server on :8000
fledge run serve-bun        # bunx serve alternative
fledge run validate         # html-validate on index.html
fledge lanes run ci         # format-check + validate + links
```

Open `http://localhost:8000` in your browser.

> Don't have fledge yet? `cargo install fledge` or `brew install CorvidLabs/tap/fledge`.

## Deploy

Pushes to `main` auto-deploy via GitHub Pages.

## Links

- [Fledge repo](https://github.com/CorvidLabs/fledge)
- [Documentation](https://corvidlabs.github.io/fledge/)
- [Plugins](https://github.com/orgs/CorvidLabs/repositories?q=fledge-plugin)
