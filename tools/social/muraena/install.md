---
tool: "muraena"
build-system: "go"
upstream: "https://github.com/muraenateam/muraena"
recipe: "blackarch"
source: "https://github.com/muraenateam/muraena"
license: "BSD"
blackarch-pkgver: "230.9a535ba"
---

# Installing muraena

> Almost-transparent reverse proxy to automate phishing and post-phishing activities.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/muraena) (version `230.9a535ba`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S muraena    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/muraenateam/muraena/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/muraenateam/muraena) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`

## Exact build steps from the recipe

```bash
cd $pkgname

  GOPATH="$srcdir" go mod download
  GOPATH="$srcdir" go build \
    -trimpath \
    -buildmode=pie \
    -mod=readonly \
    -modcacherw \
    -ldflags "-s -w" \
    -o $pkgname .
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[muraena]] · [[BlackArch Tools]]

