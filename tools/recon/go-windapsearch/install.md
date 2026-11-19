---
tool: "go-windapsearch"
build-system: "go"
upstream: "https://github.com/ropnop/go-windapsearch"
recipe: "blackarch"
source: "https://github.com/ropnop/go-windapsearch"
license: "BSD"
blackarch-pkgver: "v0.3.0.r22.ged05587"
---

# Installing go-windapsearch

> Utility to enumerate users, groups and computers from a Windows domain through LDAP queries.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/go-windapsearch) (version `v0.3.0.r22.ged05587`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S go-windapsearch    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/ropnop/go-windapsearch/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/ropnop/go-windapsearch) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`
- **System libraries** (Arch name — translate for your distro):
  - `mage`

## Exact build steps from the recipe

```bash
cd $pkgname

  mage build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[go-windapsearch]] · [[BlackArch Tools]]

