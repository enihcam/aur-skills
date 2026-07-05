---
name: aur-vcs-packages
description: Version Control System packages (-git, -svn, -hg) for the AUR. Covers naming, dynamic pkgver(), source handling, and best practices. Use when creating or maintaining -git/-svn/-hg packages.
license: MIT
compatibility: opencode
---

# Skill: aur-vcs-packages

## Purpose

Create and maintain VCS packages that build from live repository sources.

## Naming

- Append VCS suffix: `-git`, `-svn`, `-hg`, `-bzr`, `-darcs`
- Example: `pkgname=my-app-git`

## Source Array

```bash
source=("$pkgname::git+https://github.com/user/repo.git#branch=main")
sha256sums=('SKIP')   # VCS sources always use SKIP
```

For pinned-tag sources, prefer the **tag object hash** over the tag name (tag names can be force-pushed):

```bash
_tag=$(git rev-parse "v$pkgver")   # resolves once at build prep time
source=("git+https://github.com/user/repo.git#tag=${_tag}")

pkgver() {
  cd "$srcdir/repo"
  git describe --long --tags | sed 's/\([^-]*-g\)/r\1/;s/-/./g'
}
```

Using `pkgver()` separately from `_tag` prevents accidentally bumping `pkgver` without updating the pinned tag.

## Dynamic pkgver()

For VCS packages, define a `pkgver()` function instead of a static `pkgver`:

```bash
pkgver() {
  cd "$srcdir/$pkgname"
  git describe --long --tags 2>/dev/null | sed 's/\([^-]*-g\)/r\1/;s/-/./g' || echo "r$(git rev-list --count HEAD).$(git rev-parse --short HEAD)"
}
```

This generates a version like `1.2.3.r4.gabc123f`.

## Important Rules

- **Do NOT commit mere pkgver bumps** — only commit structural PKGBUILD changes
- VCS packages are NOT "out of date" just because upstream has new commits
- Internet connection required at build time (note this in comments)
- Use `SKIP` for all checksums on VCS sources
- Always test that `makepkg -o` (download-only) succeeds

## Related

- `aur-pkgbuild` — PKGBUILD structure
- `aur-submission` — submitting the package
