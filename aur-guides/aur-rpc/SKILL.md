---
name: aur-rpc
description: Query the Aurweb RPC interface (v5) to search AUR packages, retrieve package info, and consume the metadata archives. Use when building an AUR helper, verifying upstream state, bulk-fetching package metadata, or integrating with the AUR programmatically.
license: MIT
compatibility: opencode
---

# Skill: aur-rpc

## Purpose

Interact with the [Aurweb RPC interface](https://wiki.archlinux.org/title/Aurweb_RPC_interface) — the lightweight JSON HTTP API that powers `yay`, `paru`, the AUR web search, and most third-party tooling.

**Base URL:** `https://aur.archlinux.org/rpc/v5/`

## Query Types

Two endpoint shapes:

### Search

```
GET /rpc/v5/search/<keyword>?by=<field>
```

| `by` field | Meaning |
|------------|---------|
| `name-desc` *(default)* | Match against `Name` and `Description` |
| `name` | Match against `Name` only |
| `maintainer` | Match against `Maintainer` (empty → orphan list) |
| `comaintainers` | Match against comaintainer usernames |
| `depends` | Packages whose `Depends` contains the keyword |
| `makedepends` | Packages whose `MakeDepends` contains the keyword |
| `optdepends` | Packages whose `OptDepends` contains the keyword |
| `checkdepends` | Packages whose `CheckDepends` contains the keyword |
| `provides` | Packages whose `Provides` contains the keyword |
| `conflicts` | Packages whose `Conflicts` contains the keyword |
| `replaces` | Packages whose `Replaces` contains the keyword |
| `groups` | Match against `Groups` |
| `submitter` | Match against the submitter username |

### Info

```
GET /rpc/v5/info?arg[]=<pkg1>&arg[]=<pkg2>&...
```

URL-encode the brackets as `%5B%5D` when calling by hand.

## Response Envelope

Every response is JSON with the same shape:

```json
{
  "version": 5,
  "type": "search" | "multiinfo" | "error",
  "resultcount": 0,
  "results": [...]
}
```

`type: "error"` includes an additional `"error": "..."` string.

## Fields

### Always returned (`search` and `multiinfo`)

| Field | Type | Meaning |
|-------|------|---------|
| `ID` | int | Per-package ID |
| `Name` | str | Package name (within its base) |
| `PackageBaseID` | int | Base ID |
| `PackageBase` | str | Base name (used in URLs, git clones, requests) |
| `Version` | str | Current `pkgver-pkgrel` |
| `Description` | str | `pkgdesc` |
| `URL` | str | Upstream URL |
| `NumVotes` | int | Vote count |
| `Popularity` | float | Popularity score |
| `OutOfDate` | int \| null | Unix timestamp if flagged out-of-date, else `null` |
| `Maintainer` | str \| null | `null` if orphaned |
| `FirstSubmitted` | int | Unix timestamp |
| `LastModified` | int | Unix timestamp |
| `URLPath` | str | Snapshot URL fragment: `/cgit/aur.git/snapshot/<pkgbase>.tar.gz` |

### `multiinfo` only

| Field | Type |
|-------|------|
| `Depends` | string[] |
| `MakeDepends` | string[] |
| `OptDepends` | string[] |
| `CheckDepends` | string[] |
| `Conflicts` | string[] |
| `Provides` | string[] |
| `Replaces` | string[] |
| `Groups` | string[] |
| `License` | string[] |
| `Keywords` | string[] |

Fields a package doesn't have are **omitted** (not present as `null`).

## JSONP

Append `?callback=<name>` to wrap the response in a JS function call. Useful from browser-side code only.

## Limits

- HTTP GET URI: max **8190 bytes** (nginx with HTTP/2 enforces **4443 bytes**)
- Search keyword: min **2 characters**
- Search returns truncated at **5000 results**
- Rate limit: **4000 requests/day per IP**
- `info` with >200 packages needs to be split

## Metadata Archives (Bulk)

For repeated bulk queries, prefer the gzipped metadata archives — much friendlier to the AUR than thousands of RPC calls. All live at `https://aur.archlinux.org/<archive>.gz`, refreshed ~every 5 minutes.

| Archive | Format |
|---------|--------|
| `packages.gz` | One package name per line |
| `pkgbase.gz` | One package base per line |
| `users.gz` | One username per line |
| `packages-meta-v1.json.gz` | Full `type=search` JSON for every package |
| `packages-meta-ext-v1.json.gz` | Full `type=multiinfo` JSON (with all relation fields) |

Supports `Last-Modified` and `ETag` — use them for incremental updates.

## Examples

```bash
# Search for packages matching "neovim"
curl 'https://aur.archlinux.org/rpc/v5/search/neovim'

# Info for multiple packages (URL-encoded brackets)
curl 'https://aur.archlinux.org/rpc/v5/info?arg%5B%5D=yay&arg%5B%5D=paru'

# Out-of-date packages maintained by a user (search then filter)
curl 'https://aur.archlinux.org/rpc/v5/search/?by=maintainer' \
  | jq '.results[] | select(.OutOfDate != null) | {Name, OutOfDate, Version}'

# Bulk fetch — use the metadata archive once per refresh
curl -O https://aur.archlinux.org/packages-meta-ext-v1.json.gz
```

## Reference Implementation

The Arch Wiki links to canonical Python clients. For new helpers, prefer:

- Batch with the metadata archive, then fall back to RPC for individual package deltas
- Cache aggressively (ETag / `Last-Modified`)
- Respect the 4000/day rate limit — back off on HTTP 429

## Related

- `aur-helpers` — clients built on top of this API
- `aur-audit` — uses RPC to verify `OutOfDate`, popularity, votes before flagging
- `aur-submission` — `URLPath` is the snapshot download link for non-git clients
- Full spec: https://wiki.archlinux.org/title/Aurweb_RPC_interface
- Swagger: https://aur.archlinux.org/rpc/swagger