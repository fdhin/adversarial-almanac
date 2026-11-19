---
tool: "phishery"
build-system: "go"
upstream: "https://github.com/ryhanson/phishery"
recipe: "blackarch"
source: "https://github.com/ryhanson/phishery"
license: "custom:unknown"
blackarch-pkgver: "14.5743953"
---

# Installing phishery

> An SSL Enabled Basic Auth Credential Harvester with a Word Document Template URL Injector.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/phishery) (version `14.5743953`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S phishery    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/ryhanson/phishery/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/ryhanson/phishery) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`
  - `go-pie`

## Exact build steps from the recipe

```bash
# package unmaintained that can't be migrated to new build process
  # latest commit in 2016
  mkdir -p "src/$url" && rm -rf "src/$url" && mv $pkgname "src/$url" &&
    cd "src/$url"

  GOPATH="$srcdir" go get -d -t $url
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[backdoor]] · [[phishery]] · [[BlackArch Tools]]

