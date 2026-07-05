---
name: aur-package-guidelines
description: Arch Linux packaging standards and conventions. Covers naming, versioning, dependencies, directory layout, and licensing rules. Use when reviewing a PKGBUILD for compliance or learning Arch packaging standards.
---

# Skill: aur-package-guidelines

## Purpose

Reference for Arch Linux packaging standards and conventions.

## Naming

- Lowercase alphanumeric characters + `@ . _ + -`
- Cannot start with `-` or `.`
- Match upstream source tarball name when possible
- Suffixes: `-git`, `-svn`, `-hg`, `-bzr` (VCS); `-bin` (prebuilt)
- No version-number-as-name (e.g. not `libfoo2`)

## Versioning

- `pkgver`: upstream version, **no hyphens** (use `_`)
- `pkgrel`: starts at 1; bump for PKGBUILD-only changes; reset to 1 on new pkgver
- `epoch`: 0 default; increment only to force version ordering

## Dependencies

- **List ALL direct dependencies** — never rely on transitive deps
- Do NOT list packages from `base-devel` (gcc, make, etc.) in `makedepends`
- Use `optdepends=('pkg: description')` for optional features
- Architecture-specific: `depends_x86_64=()`

## Directory Layout

| Path | Use |
|------|-----|
| `/usr/bin` | Executables |
| `/usr/lib` | Libraries |
| `/usr/include` | Headers |
| `/usr/share` | Arch-independent data |
| `/etc` | Config files |

**Do NOT use:** `/bin`, `/sbin`, `/dev`, `/home`, `/srv`, `/media`, `/mnt`, `/proc`, `/root`, `/sys`, `/tmp`, `/var/tmp`, `/run`

## Licensing

- `license` field in PKGBUILD = SPDX identifier only (`MIT`, `GPL-3.0-or-later`, `Apache-2.0`, `BSD-3-Clause`, `0BSD`)
- For custom licenses or BSD-family: install file to `$pkgdir/usr/share/licenses/$pkgname/LICENSE`
- PKGBUILD and helper files in AUR repo should be licensed under **0BSD**

## Install Scripts (.install)

- Run on the **target system** during pacman operations
- Do NOT use `$pkgdir` or `$srcdir` — they don't exist at install time
- Functions: `pre_install`, `post_install`, `pre_upgrade`, `post_upgrade`, `pre_remove`, `post_remove`

## Architecture

- `arch=('x86_64')` for compiled packages
- `arch=('any')` for architecture-independent scripts/data
