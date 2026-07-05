---
name: aur-pkgbuild
description: Create and edit PKGBUILD files for Arch Linux packages. Covers all variables, functions, naming, versioning, sources, and validation. Use when writing a new PKGBUILD, editing an existing one, or fixing build issues.
license: MIT
compatibility: opencode
---

# Skill: aur-pkgbuild

## Purpose

Create and edit PKGBUILD files — the build scripts used by makepkg to produce Arch Linux packages.

## Required Variables

```bash
pkgname=my-app              # lowercase, alphanumeric + @._+-
pkgver=1.0.0               # no hyphens — use underscores
pkgrel=1                    # resets to 1 on new upstream version
arch=('x86_64')             # or 'any'
license=('MIT')             # SPDX identifier
```

## Optional Variables

```bash
pkgdesc="Short description"    # ~80 chars, no self-reference
url="https://example.com"
depends=('glibc>=2.35')
makedepends=('cmake' 'ninja')  # base-devel assumed present
optdepends=('cups: printing')
source=("$pkgname-$pkgver.tar.gz::https://example.com/v$pkgver.tar.gz")
sha256sums=('abc123...')
noextract=()        # skip extraction for certain sources
validpgpkeys=()     # PGP key fingerprints
```

## Functions

```bash
prepare() { cd "$srcdir/$pkgname-$pkgver"; patch -p1 -i "$srcdir/fix.patch"; }
build()   { cd "$srcdir/$pkgname-$pkgver"; make; }
check()   { cd "$srcdir/$pkgname-$pkgver"; make check; }   # optional
package() { cd "$srcdir/$pkgname-$pkgver"; make DESTDIR="$pkgdir" install; }
```

**Always quote** `"$srcdir"` and `"$pkgdir"` — unquoted paths break with spaces.

**No new variables or functions** unless prefixed with `_` (e.g. `_customvar=`) — unprefixed names can conflict with makepkg internals.

**Do NOT use makepkg subroutines** (`error`, `msg`, `msg2`, `plain`, `warning`) — they may change. Use `printf` or `echo`.

**Keep line length below ~100 characters** — wrap long `source=()`, `depends=()`, etc. across multiple lines.

Install-time messages (extra setup, post-install notes) belong in a `.install` file, not in the `package()` function.

## Naming

- **Suffixes:** `-git`, `-svn`, `-hg`, `-bzr`, `-darcs` for VCS; `-bin` for prebuilt
- No version-number suffixes (e.g. not `libfoo2` — use real package names)
- Must match upstream tarball name when possible

## Versioning

- `pkgver` = upstream version, **no hyphens** (use `_` instead)
- `pkgrel` = Arch package revision; bump for PKGBUILD-only changes, reset to 1 on new pkgver
- `epoch` = 0 by default; increment only when you must force a version to appear newer

## Sources & Checksums

```bash
# Custom filename to avoid generic downloads
source=("unique-name.tar.gz::https://example.com/download/v1.0.tar.gz")

# VCS sources — prefer tag object hash over tag name (tags can be force-pushed)
_tag=$(git rev-parse "v$pkgver")   # compute once, e.g. from a release-monitoring script
source=("git+https://github.com/user/repo.git#tag=${_tag}")

# PGP verification
validpgpkeys=('FINGERPRINT')
```

If upstream signs only commits/tags (not tarballs), verify with `gpg.ssh.allowedSignersFile` for SSH-signed tags, or via `git verify-tag` / `git verify-commit`.

`updpkgsums PKGBUILD` to regenerate checksums. Use `b2sums` or `sha512sums` over `md5sums`.

## Licensing

- PKGBUILD `license` = SPDX identifier: `MIT`, `GPL-3.0-or-later`, `Apache-2.0`, `BSD-3-Clause`, `0BSD`
- AUR repo itself (PKGBUILD + helper files) should be **0BSD** licensed — ship a `LICENSE` file containing [the canonical Arch 0BSD text](https://gitlab.archlinux.org/archlinux/devtools/-/blob/master/data/LICENSE?ref_type=heads) and a `REUSE.toml` declaring it (use `pkgctl license setup` to generate one)
- For custom/MIT/BSD of the *upstream* software — install license file into `$pkgdir/usr/share/licenses/$pkgname/`
- Run `pkgctl license check` from `devtools` to verify `REUSE.toml` compliance

## Validation

```bash
namcap PKGBUILD
namcap *.pkg.tar.zst                     # check built package
shellcheck --shell=bash --exclude=SC2034,SC2154,SC2164 PKGBUILD
makepkg --printsrcinfo > .SRCINFO       # regen before push
```

## Examples

**Autotools:** `./configure --prefix=/usr && make && make DESTDIR="$pkgdir" install`
**CMake:** `cmake -B build -S . -DCMAKE_INSTALL_PREFIX=/usr && cmake --install build`
**Python:** `python setup.py build && python setup.py install --root="$pkgdir" --optimize=1`

## Related

- `aur-package-guidelines` — standards reference
- `aur-audit` — deeper validation
- `aur-makepkg` — build process options
