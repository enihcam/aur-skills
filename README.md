# AUR Skills

OpenCode skills for Arch Linux AUR package authoring and maintenance.

## Included skills

- **aur-author** — Write PKGBUILDs, fix lint errors, and submit packages to the [Arch User Repository](https://aur.archlinux.org/).

## Install

These skills can be used per-project or installed globally.

### Per-project (recommended)

```bash
# From your project root:
git clone https://github.com/enihcam/aur-skills.git /tmp/aur-skills
ln -s /tmp/aur-skills/.opencode/skills/aur-author .opencode/skills/aur-author
```

Or if you want to vendor the skill directly:

```bash
git clone https://github.com/enihcam/aur-skills.git
cp -r aur-skills/.opencode/skills/aur-author .opencode/skills/aur-author
```

### Global (available in all projects)

```bash
git clone https://github.com/enihcam/aur-skills.git /tmp/aur-skills
mkdir -p ~/.config/opencode/skills
ln -s /tmp/aur-skills/.opencode/skills/aur-author ~/.config/opencode/skills/aur-author
```

### Via opencode.json plugin

Add the repo to your `opencode.json` `plugin` array:

```json
{
  "plugin": [
    "aur-skills@git+https://github.com/enihcam/aur-skills.git"
  ]
}
```

Then symlink the skill into your project:

```bash
ln -s node_modules/aur-skills/.opencode/skills/aur-author .opencode/skills/aur-author
```

## Usage

When working on an AUR package, your agent will automatically detect the `aur-author` skill and use it. You can also explicitly invoke it by describing your task (e.g., "write a PKGBUILD for my-app", "fix this PKGBUILD lint error", "submit this package to AUR").

## Requirements

- [OpenCode](https://opencode.ai) or compatible agent
- For AUR submission: an [AUR account](https://aur.archlinux.org) with an uploaded SSH key

## Reference

- [Arch User Repository](https://wiki.archlinux.org/title/Arch_User_Repository)
- [Arch package guidelines](https://wiki.archlinux.org/title/Arch_package_guidelines)
- [PKGBUILD man page](https://man.archlinux.org/man/PKGBUILD.5)
- [AUR submission guidelines](https://wiki.archlinux.org/title/AUR_submission_guidelines)
