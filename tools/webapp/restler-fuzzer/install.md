---
tool: "restler-fuzzer"
build-system: "scripts"
upstream: "https://github.com/microsoft/restler-fuzzer"
recipe: "blackarch"
source: "https://github.com/microsoft/restler-fuzzer"
license: "MIT"
blackarch-pkgver: "428.6d984de"
---

# Installing restler-fuzzer

> First stateful REST API fuzzing tool for automatically testing cloud services through their REST APIs and finding security and reliability bugs in these services.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/restler-fuzzer) (version `428.6d984de`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S restler-fuzzer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/microsoft/restler-fuzzer) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `dotnet-sdk`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[restler-fuzzer]] · [[BlackArch Tools]]

