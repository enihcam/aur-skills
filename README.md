# AUR Skills

> **[English](README.md) · [中文](README.zh.md)**

A collection of [Agent Skills](https://agentskills.io) for Arch Linux AUR package development — creating PKGBUILDs, auditing, building, submitting, and maintaining packages. Works with any LLM agent that reads markdown skill files (GitHub Copilot, Cursor, Claude Code, Codex, OpenCode, Gemini CLI, and more).

> Fork of [pahheb-skills](https://github.com/Pahheb/pahheb-skills) — adapted for Arch Linux AUR package development.

## Skills

Laid out for the official Agent Skills / `gh skill` convention (`skills/*/SKILL.md`).

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

### Recommended: `gh skill` (official)

Requires [GitHub CLI](https://cli.github.com/) v2.90.0+ with `gh skill` (public preview).

```bash
# Discover
gh skill search aur --owner enihcam
gh skill preview enihcam/aur-skills aur-guides

# Install everything (recommended — dispatcher + all sub-skills)
gh skill install enihcam/aur-skills --all --agent cursor --scope user

# Or only the dispatcher / a single skill
gh skill install enihcam/aur-skills aur-guides --agent cursor --scope user
gh skill install enihcam/aur-skills aur-pkgbuild --agent claude-code --scope user

# Pin to a release
gh skill install enihcam/aur-skills --all --agent cursor --scope user --pin v2.0.0

# Later updates
gh skill update --all
```

Swap `--agent` for your host (`github-copilot`, `claude-code`, `cursor`, `codex`, `opencode`, `gemini-cli`, …). Use `--scope project` to install into the current repo’s shared `.agents/skills` directory.

### One-step (any agent)

Paste the repo URL into your agent:

```
https://github.com/enihcam/aur-skills
```

Ask it to install the skills under `skills/` (prefer all of them, or at least `aur-guides` plus the sub-skills you need).

### Manual (symlink)

Clone somewhere **persistent** (not `/tmp` — many systems mount it as tmpfs and wipe it on reboot), then symlink skills into your agent's skills directory:

```bash
git clone https://github.com/enihcam/aur-skills.git ~/.local/share/aur-skills
git -C ~/.local/share/aur-skills checkout v2.0.0   # or main
mkdir -p <install-path>
# dispatcher + all sub-skills as siblings (matches gh skill --all)
for s in ~/.local/share/aur-skills/skills/*; do
  ln -sfn "$s" "<install-path>/$(basename "$s")"
done
```

| Tool | Install Path (directory containing skill folders) |
| :--- | :--- |
| OpenCode | `~/.config/opencode/skills/` |
| Claude Code | `~/.claude/skills/` |
| Gemini CLI | `~/.gemini/skills/` |
| Codex CLI | `~/.agents/skills/` |
| Cursor | `~/.cursor/skills/` |
| Windsurf | `~/.codeium/windsurf/skills/` |
| Hermes | `~/.hermes/skills/` |

Update later with `git -C ~/.local/share/aur-skills fetch --tags && git -C ~/.local/share/aur-skills checkout v2.0.0` (or `gh skill update --all`).

**Migration from v1.x:** the tree moved from repo-root `aur-guides/` to `skills/*/`. Old symlinks to `…/aur-skills/aur-guides` break; re-link from `…/aur-skills/skills/…` or reinstall with `gh skill`.

**OpenCode notes:**

- Prefer symlinks under `~/.config/opencode/skills/`. Pointing a `skills` array entry at a clone *outside* that directory can trigger a permission prompt on every skill load.
- Do **not** add this repo as an OpenCode `plugin` (`aur-skills@git+…`). Plugins are installed as npm packages; this repository has no `package.json`, so that entry fails on startup.

Per-project (OpenCode):

```bash
git clone https://github.com/enihcam/aur-skills.git ~/.local/share/aur-skills
mkdir -p .opencode/skills
for s in ~/.local/share/aur-skills/skills/*; do
  ln -sfn "$s" ".opencode/skills/$(basename "$s")"
done
```

## Usage

```
Use @aur-guides to help me create a PKGBUILD for my project
Use @aur-pkgbuild to write a PKGBUILD for my-app
Use @aur-submission to submit my package to the AUR
```

The `aur-guides` skill is the main dispatcher — it routes to the appropriate
sub-skill based on your task. Install the matching sub-skills (or use `--all`)
so those routes resolve.

## Publishing (maintainers)

```bash
gh skill publish --dry-run
gh skill publish --tag vX.Y.Z
```

This validates against the Agent Skills spec, ensures the `agent-skills` topic, and cuts a GitHub Release.

## Requirements

- An LLM agent that reads skill files, or GitHub CLI with `gh skill`
- For AUR submission: an [AUR account](https://aur.archlinux.org) with uploaded SSH key

## License

MIT
