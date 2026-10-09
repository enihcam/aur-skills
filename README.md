# AUR Skills

> **[English](README.md) · [中文](README.zh.md)**

A collection of instruction sets for Arch Linux AUR package development — creating PKGBUILDs, auditing, building, submitting, and maintaining packages. Works with any LLM agent that reads markdown skill files.

> Fork of [pahheb-skills](https://github.com/Pahheb/pahheb-skills) — adapted for Arch Linux AUR package development.

## Skills

### aur-guides (Master Dispatcher)

Routes to specialized sub-skills for every AUR task.

| Sub-skill | Purpose |
|-----------|---------|
| **aur-pkgbuild** | PKGBUILD creation and syntax |
| **aur-package-guidelines** | Arch Linux packaging standards |
| **aur-submission** | AUR submission and maintenance |
| **aur-audit** | Package auditing and validation |
| **aur-makepkg** | Build process configuration |
| **aur-vcs-packages** | Version Control System packages |
| **aur-pacman** | Pacman usage guide |
| **aur-helpers** | AUR helper tools (yay, paru) |
| **aur-rpc** | AUR web RPC interface (search, info, metadata archives) |

## Installation

### One-step (any agent)

Copy the repo URL and paste it into your agent:

```
https://github.com/enihcam/aur-skills
```

Then ask your agent to install the `aur-guides` skill from that URL. Most agents
will read the `aur-guides/` directory from GitHub directly, without cloning or
symlinking.

### Manual (symlink)

Clone somewhere **persistent** (not `/tmp` — many systems mount it as tmpfs and wipe it on reboot), then symlink `aur-guides` into your agent's skills directory:

```bash
git clone https://github.com/enihcam/aur-skills.git ~/.local/share/aur-skills
mkdir -p "$(dirname "<install-path>/aur-guides")"
ln -s ~/.local/share/aur-skills/aur-guides <install-path>/aur-guides
```

| Tool | Install Path |
| :--- | :--- |
| OpenCode | `~/.config/opencode/skills/aur-guides` |
| Claude Code | `~/.claude/skills/aur-guides` |
| Gemini CLI | `~/.gemini/skills/aur-guides` |
| Codex CLI | `~/.agents/skills/aur-guides` |
| Cursor | `~/.cursor/skills/aur-guides` |
| Windsurf | `~/.codeium/windsurf/skills/aur-guides` |

Update later with `git -C ~/.local/share/aur-skills pull`.

**OpenCode notes:**

- Prefer the symlink under `~/.config/opencode/skills/` above. Pointing a `skills` array entry at a clone *outside* that directory can trigger a permission prompt on every skill load.
- Do **not** add this repo as an OpenCode `plugin` (`aur-skills@git+…`). Plugins are installed as npm packages; this repository has no `package.json`, so that entry fails on startup.

Per-project (OpenCode):

```bash
git clone https://github.com/enihcam/aur-skills.git ~/.local/share/aur-skills
mkdir -p .opencode/skills
ln -s ~/.local/share/aur-skills/aur-guides .opencode/skills/aur-guides
```

## Usage

```
Use @aur-guides to help me create a PKGBUILD for my project
Use @aur-pkgbuild to write a PKGBUILD for my-app
Use @aur-submission to submit my package to the AUR
```

The `aur-guides` skill is the main dispatcher — it routes to the appropriate
sub-skill based on your task.

## Requirements

- An LLM agent that reads skill files (OpenCode, Claude Code, Gemini CLI, Codex, etc.)
- For AUR submission: an [AUR account](https://aur.archlinux.org) with uploaded SSH key

## License

MIT
