# AUR Skills

A collection of OpenCode skills for Arch Linux AUR package development — creating PKGBUILDs, auditing, building, submitting, and maintaining packages.

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

Symlink the `aur-guides` directory into your agent's skills path:

```bash
git clone https://github.com/enihcam/aur-skills.git /tmp/aur-skills
ln -s /tmp/aur-skills/aur-guides <install-path>/aur-guides
```

### Supported Tools

| Tool | Install Path | How to Invoke |
| :--- | :--- | :--- |
| OpenCode | `~/.config/opencode/skills/` | `Use @aur-guides to help me create...` |
| Claude Code | `~/.claude/skills/` | `Use @aur-guides to help me create...` |
| Gemini CLI | `~/.gemini/skills/` | `Use aur-guides to help me create...` |
| Codex CLI | `~/.agents/skills/` | `Use aur-guides to help me create...` |
| Cursor | `~/.cursor/skills/` | `@aur-guides help me create...` |
| Windsurf | `~/.codeium/windsurf/skills/` | `@aur-guides help me create...` |

### Per-project (OpenCode)

```bash
git clone https://github.com/enihcam/aur-skills.git /tmp/aur-skills
mkdir -p .opencode/skills
ln -s /tmp/aur-skills/aur-guides .opencode/skills/aur-guides
```

### Global (OpenCode, all projects)

```bash
git clone https://github.com/enihcam/aur-skills.git /tmp/aur-skills
mkdir -p ~/.config/opencode/skills
ln -s /tmp/aur-skills/aur-guides ~/.config/opencode/skills/aur-guides
```

### Via opencode.json plugin

```json
{
  "plugin": [
    "aur-skills@git+https://github.com/enihcam/aur-skills.git"
  ]
}
```

Then symlink from `node_modules/aur-skills/aur-guides` into your skills path.

## Usage

```
Use @aur-guides to help me create a PKGBUILD for my project
Use @aur-pkgbuild to write a PKGBUILD for my-app
Use @aur-submission to submit my package to the AUR
```

The `aur-guides` skill is the main dispatcher — it routes to the appropriate sub-skill based on your task.

## Requirements

- [OpenCode](https://opencode.ai), [Claude Code](https://docs.anthropic.com/en/docs/claude-code/overview), or compatible agent
- For AUR submission: an [AUR account](https://aur.archlinux.org) with uploaded SSH key

## License

MIT
