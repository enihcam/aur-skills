---
name: aur-audit
description: Audit and validate PKGBUILDs before AUR submission. Covers namcap, shellcheck, chroot testing, and common error fixes. Use when running validation, fixing lint errors, or pre-submission quality checks.
---

# Skill: aur-audit

## Purpose

Audit and validate PKGBUILDs before building or submitting to AUR.

## Validation Commands

```bash
namcap PKGBUILD                          # check PKGBUILD
namcap *.pkg.tar.zst                     # check built package
shellcheck --shell=bash --exclude=SC2034,SC2154,SC2164 PKGBUILD
makepkg --printsrcinfo > .SRCINFO
```

## Common Errors

| Error | Fix |
|-------|-----|
| Missing `depends` | `ldd` on binaries or `namcap` on built pkg |
| Missing `makedepends` | Add build-time deps (`base-devel` is assumed) |
| `sha256sums` mismatch | Run `updpkgsums` |
| `pkgver` has hyphen | Replace with underscore |
| Bad `license` format | Use SPDX: `MIT`, `GPL-3.0-or-later`, `BSD-3-Clause`, `0BSD` |
| `source` URL unreachable | Verify URL; use `name::url` syntax to rename |
| `pkgdesc` self-referencing | "An editor for X" not "X is an editor for" |
| Missing `.SRCINFO` | `makepkg --printsrcinfo > .SRCINFO` |

## Chroot Testing

```bash
pkgctl build       # builds in clean chroot via systemd-nspawn
```

Catches issues that work on your system but fail in clean environments.

## Pre-submission Checklist

- [ ] `namcap PKGBUILD` — no errors
- [ ] `namcap *.pkg.tar.zst` — no errors (if built)
- [ ] `shellcheck PKGBUILD` with standard exclusions
- [ ] `.SRCINFO` regenerated
- [ ] `makepkg -s` builds successfully
- [ ] `makepkg --check` tests pass (if applicable)

## Related

- `aur-pkgbuild` — fixing found issues
- `aur-submission` — push after audit passes
