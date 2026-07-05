---
name: aur-submission
description: Submit and maintain packages in the Arch User Repository. Covers SSH setup, Git workflow, .SRCINFO, maintenance, and request types. Use when submitting a new package, updating an existing one, or requesting deletion/merge/orphan.
---

# Skill: aur-submission

## Purpose

Submit, update, and maintain packages in the Arch User Repository.

## Submission Rules

- Package must NOT exist in official repos (core/extra/community)
- Must be useful, unique, and x86_64-compatible
- Must include a LICENSE file (0BSD recommended for PKGBUILD)
- Prebuilt binaries allowed only with `-bin` suffix
- Modified official packages need different `pkgname` + `conflicts`/`provides`

## SSH Setup

```bash
ssh-keygen -t ed25519 -C "aur@archlinux.org"
```

Add to `~/.ssh/config`:
```
Host aur.archlinux.org
  User aur
  IdentityFile ~/.ssh/aur
```

Upload public key at https://aur.archlinux.org/account/. Test: `ssh aur@aur.archlinux.org`

## Git Workflow

**New package:**
```bash
git -c init.defaultBranch=master clone ssh://aur@aur.archlinux.org/pkgname.git
cp /path/to/PKGBUILD .SRCINFO LICENSE .   # include patches, .install files
git add PKGBUILD .SRCINFO LICENSE
git commit -m "Initial release: pkgname pkgver-pkgrel"
git push origin master
```

**Update existing:**
```bash
git clone ssh://aur@aur.archlinux.org/pkgname.git   # or git pull
# edit PKGBUILD
updpkgsums PKGBUILD
makepkg --printsrcinfo > .SRCINFO
git add PKGBUILD .SRCINFO
git commit -m "Update to version X.Y.Z"
git push
```

Always push to `master` branch. Use meaningful commit messages.

## .SRCINFO

**Mandatory** — AUR web interface reads metadata from this file, not PKGBUILD. Regenerate after every metadata change:

```bash
makepkg --printsrcinfo > .SRCINFO
```

## Maintainer Attribution

```bash
# Maintainer: Your Name <you at example dot com>
# Contributor: Previous Person <old at example dot com>
```

Email obfuscation: `user at example dot com` format. When adopting, move previous maintainer to Contributor.

## Maintenance

- Respond to comments, update regularly, fix build failures
- Do NOT submit-and-forget — actively disown if you can't maintain
- Flag package as out-of-date when upstream releases new version

## Request Types

- **Deletion:** broken beyond repair, already in official repos, license issues
- **Merge:** package renamed or absorbed by another
- **Orphan:** maintainer unresponsive — auto-accepted if flagged out-of-date ≥180 days

## Pre-flight Checklist

- [ ] `pacman -Ss pkgname` — verify not in official repos
- [ ] `makepkg -s` — builds successfully
- [ ] `namcap PKGBUILD` and `namcap *.pkg.tar.zst` — no errors
- [ ] `shellcheck PKGBUILD` — passes
- [ ] `.SRCINFO` regenerated
- [ ] LICENSE file included
- [ ] Maintainer line present, email obfuscated

## Related

- `aur-pkgbuild` — creating the PKGBUILD
- `aur-audit` — validation before push
