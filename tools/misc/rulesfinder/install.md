---
tool: "rulesfinder"
build-system: "go"
upstream: "https://github.com/synacktiv/rulesfinder"
recipe: "blackarch"
source: "https://github.com/synacktiv/rulesfinder"
license: "custom:unknown"
blackarch-pkgver: "42.cf8255b"
---

# Installing rulesfinder

> Machine-learn password mangling rules.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rulesfinder) (version `42.cf8255b`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S rulesfinder    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/synacktiv/rulesfinder/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/synacktiv/rulesfinder) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `cargo`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `gcc-libs`

## Exact build steps from the recipe

```bash
cd $pkgname

  cargo build --release
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[rulesfinder]] · [[BlackArch Tools]]

