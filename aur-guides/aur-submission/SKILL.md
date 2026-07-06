---
name: aur-submission
description: Submit and maintain packages in the Arch User Repository. Covers SSH setup, Git workflow, .SRCINFO, maintenance, and request types. Use when submitting a new package, updating an existing one, or requesting deletion/merge/orphan.
license: MIT
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
  # Required for git history rewrite (AUR_OVERWRITE)
  SendEnv AUR_OVERWRITE
```

Upload public key at https://aur.archlinux.org/account/. Test: `ssh aur@aur.archlinux.org`

## Package Base vs Package

Each AUR repo is a **package base** (`PackageBase`). A base can produce one or more **packages** (split builds). The RPC returns `PackageBaseID` (base) and `Name` (individual package). Always reference the base name in URLs, git clones, and requests.

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

## License File (0BSD)

Every AUR repo must contain:

1. **`LICENSE`** — copy of the [canonical Arch 0BSD text](https://gitlab.archlinux.org/archlinux/devtools/-/blob/master/data/LICENSE?ref_type=heads)
2. **`REUSE.toml`** — declare the license for each file (generate with `pkgctl license setup`, verify with `pkgctl license check`)

## Rewriting Git History

To rewrite history (e.g. removing a legal name from past commits), use `git-filter-repo` and force-push with `AUR_OVERWRITE=1`:

```bash
git-filter-repo --name-callback 'return name.replace(b"Old Name", b"New Name")' \
                --email-callback 'return email.replace(b"old@x", b"new@x")'

AUR_OVERWRITE=1 git push --force
```

Make a backup first.

## SSH Voting

With SSH auth set up, you can vote directly:

```bash
ssh aur@aur.archlinux.org vote <pkgname>
```

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
- Flag package as out-of-date when upstream releases a new version (provide links)
- **Never flag VCS packages as out-of-date** purely because `pkgver` changed — they aren't considered out-of-date just because upstream moved
- Try to reach the maintainer by email before requesting an orphan; orphan requests can be filed after **14 days** of no response from the maintainer and no package update
- Orphan requests auto-accept after the package has been flagged out-of-date for **180 days**

## Request Types

- **Deletion:** broken beyond repair, already in official repos, license issues
- **Merge:** package renamed or absorbed by another
- **Orphan:** maintainer unresponsive — auto-accepted if flagged out-of-date ≥180 days

## Pre-flight Checklist

- [ ] `pacman -Ss pkgname` — verify not in official repos
- [ ] `makepkg -s` — builds successfully
- [ ] `makepkg --check` — tests pass (if `check()` defined)
- [ ] `namcap PKGBUILD` and `namcap *.pkg.tar.zst` — no errors
- [ ] `shellcheck PKGBUILD` — passes
- [ ] `.SRCINFO` regenerated (`makepkg --printsrcinfo > .SRCINFO`)
- [ ] `LICENSE` (0BSD) + `REUSE.toml` included (`pkgctl license check` clean)
- [ ] Maintainer line present, email obfuscated
- [ ] Review PKGBUILD for malicious or dangerous commands (use `traur` / `ks-aur-scanner`)

## Related

- `aur-pkgbuild` — creating the PKGBUILD
- `aur-audit` — validation before push
