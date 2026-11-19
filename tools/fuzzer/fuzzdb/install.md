---
tool: "fuzzdb"
build-system: "scripts"
upstream: "https://github.com/fuzzdb-project/fuzzdb"
recipe: "blackarch"
nix-package: "fuzzdb"
source: "https://github.com/fuzzdb-project/fuzzdb"
license: "BSD"
blackarch-pkgver: "475.5656ab2"
---

# Installing fuzzdb

> Attack and Discovery Pattern Dictionary for Application Fault Injection Testing.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/fuzzdb) (version `475.5656ab2`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S fuzzdb    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/fuzzdb-project/fuzzdb) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[fuzzdb]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#fuzzdb    # ad-hoc run, no install
nix profile install nixpkgs#fuzzdb   # or persist it
```
