---
tool: "dirble"
build-system: "go"
upstream: "https://github.com/nccgroup/dirble"
recipe: "blackarch"
aur-package: "dirble"
source: "https://github.com/nccgroup/dirble"
license: "GPL3"
blackarch-pkgver: "1.4.2"
---

# Installing dirble

> Fast directory scanning and scraping tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dirble) (version `1.4.2`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S dirble    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/nccgroup/dirble/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/nccgroup/dirble) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `cargo`
- **System libraries** (Arch name — translate for your distro):
  - `curl`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  cargo build --release
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[dirble]] · [[BlackArch Tools]]

