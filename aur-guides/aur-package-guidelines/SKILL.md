---
name: aur-package-guidelines
description: Arch Linux packaging standards and conventions. Covers naming, versioning, dependencies, directory layout, and licensing rules. Use when reviewing a PKGBUILD for compliance or learning Arch packaging standards.
license: MIT
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
| `/etc` | System-essential configuration files |
| `/etc/_pkg_` | Per-package configuration files (use a subdir when there are multiple) |
| `/usr/bin` | Executables |
| `/usr/lib` | Libraries |
| `/usr/include` | Header files |
| `/usr/lib/_pkg_` | Modules, plugins, internals (avoid `/usr/libexec/` — use this instead) |
| `/usr/share/doc/_pkg_` | Application documentation |
| `/usr/share/info` | GNU Info system files |
| `/usr/share/licenses/_pkg_` | Application licenses |
| `/usr/share/man` | Manpages |
| `/usr/share/_pkg_` | Application data |
| `/var/lib/_pkg_` | Persistent application storage |
| `/opt/_pkg_` | Large self-contained packages |

**Never install to `/usr/local/`** — it is reserved for the system administrator.

**Do NOT use:** `/bin`, `/sbin`, `/dev`, `/home`, `/srv`, `/media`, `/mnt`, `/proc`, `/root`, `/selinux`, `/sys`, `/tmp`, `/var/tmp`, `/run`

## PKGBUILD Hygiene

- **Do not introduce new variables or functions** in `PKGBUILD` unless the package cannot be built without them — names may conflict with makepkg internals
- If a new variable/function is truly required, **prefix with `_`** (e.g. `_customvar=`)
- **Do NOT use makepkg subroutines** (`error`, `msg`, `msg2`, `plain`, `warning`) — they may change at any time. Use `printf` or `echo`
- **Avoid `/usr/libexec/`** — use `/usr/lib/$pkgname/` instead
- **Quote variables** that may contain spaces: `"$pkgdir"`, `"$srcdir"`
- **Keep line length under ~100 characters**; remove empty lines from arrays (`provides=()`, `replaces=()`, etc.)
- Preserve the conventional order of `PKGBUILD` fields (see PKGBUILD man page) when practical
- Descriptions: ~80 chars max, no self-reference ("A text editor" not "Foo is a text editor")
- `packager` field is customizable in `/etc/makepkg.conf` or `~/.makepkg.conf`

## Dependencies

- **Do not rely on transitive dependencies** — list every direct library dep
- Use `find-libdeps(1)` (from `devtools`) to identify direct library deps
- Optional features belong in `optdepends=('pkg: short reason')`, NOT in `depends`
- `base-devel` is assumed present — don't list `gcc`, `make`, etc. in `makedepends`

## Relations

- Do NOT add `$pkgname` to `provides` (implicitly provided)
- Do NOT add `$pkgname` to `conflicts` (a package cannot conflict with itself)
- External shared libs go in `provides=('libsomething.so')` — use `find-libprovides(1)` (from `devtools`)

## Sources

- **HTTPS** for tarballs; `git+https://` for git sources
- **PGP verification** wherever possible — fetch keys listed in `validpgpkeys=()`
- For git tag sources, use the **tag object hash** (`git rev-parse "v$pkgver"`) — tag names can be force-pushed
- Sources must be unique in `$srcdir` (use `name::url` syntax to rename if needed)
- Avoid mirrors (SourceForge, etc.) — they may disappear
- Don't strip PGP/checksum checks just because upstream forgot to sign a release

## Licensing

There are two independent license scopes:

### Packaged software

- PKGBUILD `license=()` describes the **upstream software**, not the PKGBUILD or AUR repository.
- Use an [SPDX license expression](https://rfc.archlinux.page/0016-spdx-license-identifiers/): `license=('MIT')`, `license=('MIT OR Apache-2.0')`, or `license=('GPL-2.0-only WITH Classpath-exception-2.0')`. Put a composite expression in one quoted array element.
- For a license absent from the SPDX list, use `LicenseRef-name` or `custom:name` and install its text under `$pkgdir/usr/share/licenses/$pkgname/`.
- Every declared license needs corresponding text under `/usr/share/licenses`. The `licenses` package supplies common exact texts. Ship a package-specific copy for custom licenses and license families with varying texts, notably MIT and BSD.

### Package sources

- The PKGBUILD, `.install` files, patches, and other AUR Git files have their own licenses. Do not infer these from `license=()`.
- The [AUR submission guidelines](https://wiki.archlinux.org/title/AUR_submission_guidelines#Rules_of_submission) require a package-source `LICENSE` and/or `REUSE.toml` and encourage 0BSD.
- [RFC 0040](https://rfc.archlinux.page/0040-license-package-sources/#aur) explicitly does **not** require every AUR package to use 0BSD. A package without the expected 0BSD license is not eligible for promotion to the official repositories.
- For the recommended promotion-compatible 0BSD + REUSE setup, run `pkgctl license setup`, review annotations for third-party files, then run `pkgctl license check`. Do not relabel upstream files or patches as 0BSD.

## Reproducible Builds

Arch is moving toward reproducible builds. Verify with:

```bash
makerepropkg pkgname-1-1-x86_64.pkg.tar.zst    # from devtools
repro -f pkgname-1-1-x86_64.pkg.tar.zst        # from archlinux-repro
```

If build-time timestamps are required, set `SOURCE_DATE_EPOCH` (see [reproducible-builds.org](https://reproducible-builds.org/docs/source-date-epoch/)).

## Install Scripts (.install)

- Run on the **target system** during pacman operations
- Do NOT use `$pkgdir` or `$srcdir` — they don't exist at install time
- Functions: `pre_install`, `post_install`, `pre_upgrade`, `post_upgrade`, `pre_remove`, `post_remove`
- Important messages (post-install setup steps) belong here, not in `package()`

## Architecture

- `arch=('x86_64')` for compiled packages
- `arch=('any')` for architecture-independent scripts/data
