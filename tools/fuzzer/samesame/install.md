---
tool: "samesame"
build-system: "go"
upstream: "https://github.com/TheTarquin/samesame"
recipe: "blackarch"
source: "https://github.com/TheTarquin/samesame"
license: "custom:IDGAD"
blackarch-pkgver: "68.a9bcd7b"
---

# Installing samesame

> Command line tool to generate crafty homograph strings.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/samesame) (version `68.a9bcd7b`, license custom:IDGAD).

## Arch / BlackArch

```bash
sudo pacman -S samesame    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/TheTarquin/samesame/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/TheTarquin/samesame) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `cargo`, `git`

## Exact build steps from the recipe

```bash
cd $pkgname

  cargo build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[samesame]] · [[BlackArch Tools]]

