---
tool: "lorsrf"
build-system: "go"
upstream: "https://github.com/knassar702/lorsrf"
recipe: "blackarch"
source: "https://github.com/knassar702/lorsrf"
license: "custom:unknown"
blackarch-pkgver: "v2.1.r3.g5c69453"
---

# Installing lorsrf

> Find the parameters that can be used to find SSRF or Out-of-band resource load.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/lorsrf) (version `v2.1.r3.g5c69453`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S lorsrf    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/knassar702/lorsrf/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/knassar702/lorsrf) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `cargo`, `git`

## Exact build steps from the recipe

```bash
cd $pkgname

  cargo build --release
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[lorsrf]] · [[BlackArch Tools]]

