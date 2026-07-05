---
name: aur-author
description: Author AUR packages and submit them to the Arch Linux User Repository. Use when writing a PKGBUILD, fixing PKGBUILD lint errors, submitting a package to AUR, or reviewing AUR packaging best practices.
---

# AUR Package Authoring

## Quick start — minimal PKGBUILD

```bash
# Maintainer: Your Name <you@example.com>
# Contributor: Other Name <other@example.com>

pkgname=example-app
pkgver=1.0.0
pkgrel=1
pkgdesc="A short description of the package"
arch=('x86_64')
url="https://example.com"
license=('MIT')
depends=('glibc')
source=("$pkgname-$pkgver.tar.gz::https://example.com/download/v$pkgver.tar.gz")
sha256sums=('0000000000000000000000000000000000000000000000000000000000000000')

build() {
  cd "$srcdir/$pkgname-$pkgver"
  make
}

package() {
  cd "$srcdir/$pkgname-$pkgver"
  make DESTDIR="$pkgdir" install
}
```

## Workflows

### 1. Write a PKGBUILD from scratch

1. Set required variables: `pkgname`, `pkgver`, `pkgrel` (starts at 1), `pkgdesc` (~80 chars, no self-reference), `arch`, `url`, `license` (SPDX format), `depends`, `source`
2. Set integrity variables: `sha256sums` (or `b2sums`/`sha512sums`). Use `updpkgsums` to auto-update.
3. Implement functions:
   - `prepare()` — apply patches, run `cd "$srcdir/$pkgname-$pkgver" && ...`
   - `build()` — compile: `cd "$srcdir/$pkgname-$pkgver" && make`
   - `check()` — run tests (optional, use `make check` or equivalent)
   - `package()` — install: `cd "$srcdir/$pkgname-$pkgver" && make DESTDIR="$pkgdir" install`
4. Add `.install` files for post-install messages if needed (e.g., `pkgname.install` with `post_install()`)
5. Validate with `namcap PKGBUILD`
6. Run `makepkg -s` to test-build; fix errors until it succeeds

**Rules of submission:**
- No binary files in the source array (pre-compiled binaries, shared objects, etc.)
- No `-git`/`-svn` suffixes without a corresponding VCS `pkgver()` function
- HTTPS sources preferred, PGP signature verification whenever possible
- Package sources (PKGBUILD, .install, patches) licensed under 0BSD per Arch policy

### 2. Fix common PKGBUILD lint errors

Run `namcap PKGBUILD` and `namcap <pkgname>-<pkgver>-<arch>.pkg.tar.zst` on the built package.

| Error | Fix |
|-------|-----|
| Missing `depends` | Run `ldd` on binaries, `find-libdeps`, or `namcap` on the built package |
| Missing `makedepends` | Add build-time deps (gcc, cmake, meson, etc.) — note `base-devel` is assumed present |
| Missing `optdepends` | Move optional deps to `optdepends=('pkg: description')` array |
| `arch` wrong | Use `'x86_64'` for arch-specific, `'any'` for noarch |
| `source` URL unreachable | Verify upstream HTTPS URL works. Use `filename::url` syntax to rename |
| `sha256sums` mismatch | Run `updpkgsums` to re-generate after source change |
| `pkgver` contains hyphen | Replace hyphens with underscores in `pkgver` |
| Bad `license` format | Use SPDX identifier (e.g., `MIT`, `GPL2`, `Apache-2.0`, `BSD-3-Clause`, `0BSD`) |
| `pkgdesc` self-referencing | "An editor for X11" not "ExampleEditor is an editor for X11" |

### 3. Submit a package to AUR

Prerequisites: AUR account (aur.archlinux.org), SSH key uploaded to account profile.

1. Create the PKGBUILD, `.SRCINFO`, and any required files (`.install`, patches, systemd units)
2. Generate `.SRCINFO`: `makepkg --printsrcinfo > .SRCINFO`
3. Initialize local repo:
   ```bash
   git init
   git add PKGBUILD .SRCINFO <any-other-files>
   git commit -m "Initial commit: $pkgname $pkgver"
   ```
4. Push to AUR:
   ```bash
   git remote add aur ssh://aur@aur.archlinux.org/<pkgname>.git
   git push aur master
   ```
5. Verify at `https://aur.archlinux.org/packages/<pkgname>`
6. **Updating**: bump `pkgver` or `pkgrel`, update checksums, rebuild `.SRCINFO`, commit, and `git push aur master`

**Important:** SSH to the AUR is only allowed via `ssh://aur@aur.archlinux.org`. The `aur` user is the AUR's own SSH service account, not your personal Linux login.

**See also:** [AUR submission guidelines](https://wiki.archlinux.org/title/AUR_submission_guidelines), [.SRCINFO](https://wiki.archlinux.org/title/.SRCINFO), [Aurweb RPC](https://wiki.archlinux.org/title/Aurweb_RPC_interface) for programmatic querying.

## Reference docs

- [Arch User Repository](https://wiki.archlinux.org/title/Arch_User_Repository) — overview and FAQ
- [Arch package guidelines](https://wiki.archlinux.org/title/Arch_package_guidelines) — naming, versioning, deps, directories, licenses
- [PKGBUILD man page](https://man.archlinux.org/man/PKGBUILD.5) — full variable/function reference
- [Package Maintainer guidelines](https://wiki.archlinux.org/title/Package_Maintainer_guidelines) — maintainer processes
- [Creating packages](https://wiki.archlinux.org/title/Creating_packages) — general guide
