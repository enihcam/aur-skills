---
name: aur-pacman
description: Manage packages with pacman on Arch Linux. Covers install, remove, query, search, and cache management. Use when installing, removing, querying, or troubleshooting packages on an Arch system.
license: MIT
compatibility: opencode
---

# Skill: aur-pacman

## Purpose

Manage Arch Linux packages with pacman — the package manager.

## Basic Operations

```bash
pacman -Syu              # full system update
pacman -S pkgname        # install package
pacman -R pkgname        # remove package
pacman -Rs pkgname       # remove + unused dependencies
pacman -U /path/to/pkg.tar.zst  # install local file
```

## Search & Query

```bash
pacman -Ss keyword       # search remote repos
pacman -Qs keyword       # search installed packages
pacman -Si pkgname       # remote package info
pacman -Qi pkgname       # installed package info
pacman -Ql pkgname       # files owned by package
pacman -Qo /path/file    # which package owns file
pacman -Fl pattern       # files in remote package (requires pkgfile)
pacman -Qdt              # orphaned packages
pacman -Qmq              # foreign packages (typically AUR)
```

## Cache Management

```bash
paccache -r              # remove old cached versions (keep last 3)
pacman -Sc               # remove unused cached packages
pacman -Scc              # clean everything from cache
```

## Useful Flags

- `--noconfirm` — skip prompts
- `--needed` — don't reinstall up-to-date packages
- `--overwrite glob` — overwrite conflicting files
- `--print` — print what would be done (dry run)

## Database

```bash
pacman -Syy              # force refresh all databases
pacman-key --init        # initialize keyring
pacman-key --populate    # load default keys
```

## Related

- `aur-helpers` — yay, paru wrappers
