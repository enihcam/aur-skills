---
name: aur-makepkg
description: Configure and use makepkg to build Arch Linux packages. Covers flags, options arrays, environment variables, and chroot builds. Use when building packages, troubleshooting build failures, or configuring build settings.
---

# Skill: aur-makepkg

## Purpose

Build Arch Linux packages with makepkg, configure build options, and troubleshoot failures.

## Basic Usage

```bash
makepkg                 # build package
makepkg -s              # sync + install dependencies
makepkg -i              # build + install
makepkg -r              # clean after build
makepkg --check         # run check() function
makepkg -o              # download + prepare only
makepkg -e              # skip extract, resume build
makepkg -c              # clean up leftover files
makepkg --printsrcinfo  # generate .SRCINFO
```

## Options Array in PKGBUILD

```bash
options=('!emptydirs'   # remove empty dirs (default: keep)
         '!strip'       # keep debug symbols (default: strip)
         '!libtool'     # remove .la files (default: keep)
         '!ccache'      # disable ccache (default: use if installed)
         'zipman'       # compress man pages
         '!debug')      # disable debug package
```

## Environment Variables

```bash
PKGDEST=~/packages      # where .pkg.tar.zst files go
SRCDEST=~/sources       # where downloaded sources are cached
SRCPKGDEST=~/srcpkg     # where .src.tar.gz goes
MAKEFLAGS="-j$(nproc)"  # parallel build jobs
```

Set in `~/.makepkg.conf` (user) or `/etc/makepkg.conf` (system).

## Chroot Builds

```bash
pkgctl build            # builds in clean chroot via systemd-nspawn
```

Use chroot builds to verify the package builds from scratch in a clean environment.

## Common Failures

- **Missing deps:** add to `makedepends` or `depends`
- **Network required at build:** unacceptable for official repos; note in comments for AUR
- **Outdated checksums:** run `updpkgsums`
- **Parallel build failure:** set `MAKEFLAGS="-j1"` or add `!parallelsmake` to options

## Related

- `aur-pkgbuild` — PKGBUILD structure
- `aur-audit` — validation after build
