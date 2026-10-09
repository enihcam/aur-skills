---
name: aur-pkgbuild
description: Create and edit PKGBUILD files for Arch Linux packages. Covers all variables, functions, naming, versioning, sources, and validation. Use when writing a new PKGBUILD, editing an existing one, or fixing build issues.
license: MIT
---

# Skill: aur-pkgbuild

## Purpose

Create and edit PKGBUILD files — the build scripts used by makepkg to produce Arch Linux packages.

## Required Variables

```bash
pkgname=my-app              # lowercase, alphanumeric + @._+-
pkgver=1.0.0                # no hyphens — use underscores
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
checkdepends=('python-pytest') # only needed when check() runs
optdepends=('cups: printing')
source=("$pkgname-$pkgver.tar.gz::https://example.com/v$pkgver.tar.gz")
b2sums=('abc123...')           # prefer b2 or sha512 over weaker hashes
noextract=()                   # skip extraction for certain sources
validpgpkeys=()                # full uppercase PGP fingerprints, no spaces
install=$pkgname.install       # optional install script (not in source=)
changelog=$pkgname.changelog   # optional changelog (not in source=)
```

## Functions

```bash
prepare() { cd "$srcdir/$pkgname-$pkgver"; patch -p1 -i "$srcdir/fix.patch"; }
build()   { cd "$srcdir/$pkgname-$pkgver"; make; }
check()   { cd "$srcdir/$pkgname-$pkgver"; make check; }   # optional
package() { cd "$srcdir/$pkgname-$pkgver"; make DESTDIR="$pkgdir" install; }
verify()  { … }   # optional; runs before checksum/PGP checks (makepkg --noverify skips it)
```

**Always quote** `"$srcdir"` and `"$pkgdir"` — unquoted paths break with spaces.

**No new variables or functions** unless prefixed with `_` (e.g. `_customvar=`) — unprefixed names can conflict with makepkg internals.

**Do NOT use makepkg subroutines** (`error`, `msg`, `msg2`, `plain`, `warning`) — they may change. Use `printf` or `echo`.

**Keep line length below ~100 characters** — wrap long `source=()`, `depends=()`, etc. across multiple lines.

Install-time messages (extra setup, post-install notes) belong in a `.install` file, not in the `package()` function.

## Naming

- **Suffixes:** `-git`, `-svn`, `-hg`, `-bzr`, `-darcs`, `-cvs` for VCS; `-bin` for prebuilt
- No version-number suffixes (e.g. not `libfoo2` — use real package names)
- Must match upstream tarball name when possible

## Versioning

- `pkgver` = upstream version, **no hyphens** (use `_` instead); also no colons, slashes, or whitespace
- `pkgrel` = Arch package revision; bump for PKGBUILD-only changes, reset to 1 on new pkgver
- `epoch` = 0 by default; increment only when you must force a version to appear newer

## Sources & Checksums

```bash
# Custom filename to avoid generic downloads
source=("unique-name.tar.gz::https://example.com/download/v1.0.tar.gz")

# Pinned git tag: store the tag *object* hash (computed offline), not a live command
_tag=1234567890abcdef1234567890abcdef12345678  # git rev-parse "v$pkgver"
source=("git+https://github.com/user/repo.git?signed#tag=$_tag")

# PGP verification of detached signatures (.sig / .sign / .asc)
validpgpkeys=('FINGERPRINT')
```

Do **not** write `_tag=$(git rev-parse …)` in the PKGBUILD — that runs at parse time before the repo is cloned. Pin the hash once (comment how you obtained it), then bump `_tag` together with `pkgver`.

If upstream signs only commits/tags (not tarballs), verify with `gpg.ssh.allowedSignersFile` for SSH-signed tags, or via `git verify-tag` / `git verify-commit`.

`updpkgsums PKGBUILD` to regenerate checksums. Prefer `b2sums` or `sha512sums` over `md5sums` / `sha1sums`.

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

**CMake:** `cmake -B build -S . -DCMAKE_INSTALL_PREFIX=/usr && cmake --build build && cmake --install build --destdir "$pkgdir"`

**Python (PEP 517 — preferred):**

```bash
makedepends=('python-build' 'python-installer' 'python-wheel')  # + the build backend

build() {
  cd "$_name-$pkgver"
  python -m build --wheel --no-isolation
}

package() {
  cd "$_name-$pkgver"
  python -m installer --destdir="$pkgdir" dist/*.whl
}
```

Only fall back to `python setup.py build` / `install --root="$pkgdir"` when the project has no usable `pyproject.toml` build backend (deprecated; emits `SetuptoolsDeprecationWarning`).

## Related

- `aur-package-guidelines` — standards reference
- `aur-audit` — deeper validation
- `aur-makepkg` — build process options
- `aur-vcs-packages` — live VCS / `-git` packages
