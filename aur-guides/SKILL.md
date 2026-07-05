---
name: aur-guides
description: Master skill for AUR (Arch User Repository) package development. Dispatches to specialized sub-skills for creating PKGBUILDs, auditing packages, submitting to AUR, and more. Use when working with AUR packages, PKGBUILDs, makepkg, pacman, or any Arch Linux packaging task.
---

# Skill: aur-guides

## Purpose

Master dispatcher for AUR package development. Routes to the right sub-skill for your task.

## When NOT to Use

- Official Arch packages (use Package Maintainer workflow)
- Non-Arch Linux distributions
- Flatpak/Snap/AppImage packaging

## Quick Reference: Which Skill to Use

| Task | Sub-skill |
|------|-----------|
| Create/edit PKGBUILD | `aur-pkgbuild` |
| Packaging standards & guidelines | `aur-package-guidelines` |
| Submit package to AUR | `aur-submission` |
| Audit/validate with namcap | `aur-audit` |
| Build process & makepkg config | `aur-makepkg` |
| VCS packages (-git, -svn, -hg) | `aur-vcs-packages` |
| Pacman usage & queries | `aur-pacman` |
| AUR helpers (yay, paru) | `aur-helpers` |

## Complete AUR Package Lifecycle

```
1. Plan requirements
2. Create PKGBUILD        → aur-pkgbuild
3. Verify guidelines       → aur-package-guidelines
4. Build & test            → aur-makepkg
5. Audit                   → aur-audit
6. Submit to AUR           → aur-submission
7. Maintain & update       → aur-submission
```

## Workflow Examples

**New package:** `aur-pkgbuild` → `aur-package-guidelines` → `aur-makepkg` → `aur-audit` → `aur-submission`

**Update existing:** `aur-pkgbuild` → `aur-makepkg` → `aur-audit` → `aur-submission`

**VCS package:** `aur-vcs-packages` → `aur-pkgbuild` → `aur-audit` → `aur-submission`

## Key Resources

- [Arch Wiki](https://wiki.archlinux.org/)
- [AUR](https://aur.archlinux.org/)
- [PKGBUILD man page](https://man.archlinux.org/man/PKGBUILD.5)
- [Arch package guidelines](https://wiki.archlinux.org/title/Arch_package_guidelines)
- [AUR submission guidelines](https://wiki.archlinux.org/title/AUR_submission_guidelines)

## Essential Commands

```bash
makepkg -s              # Build + install deps
makepkg -i              # Build + install package
namcap PKGBUILD         # Audit PKGBUILD
updpkgsums PKGBUILD     # Update checksums
makepkg --printsrcinfo > .SRCINFO  # Generate .SRCINFO
shellcheck --shell=bash --exclude=SC2034,SC2154,SC2164 PKGBUILD
```

## Best Practices

1. **HTTPS** for all source URLs
2. **List all direct** dependencies (no transitive reliance)
3. **b2 or sha512** checksums preferred
4. **SPDX license identifiers** (GPL-3.0-or-later, MIT, BSD-3-Clause)
5. **Quote** "$pkgdir" and "$srcdir" everywhere
6. **Regenerate .SRCINFO** before every push
7. **Use :: syntax** in source array for unique filenames
8. **License PKGBUILD under 0BSD**
