---
tool: "sharpfuzz"
build-system: "scripts"
upstream: "https://github.com/Metalnem/sharpfuzz"
recipe: "blackarch"
source: "https://github.com/Metalnem/sharpfuzz"
license: "MIT"
blackarch-pkgver: "v2.2.0.r9.g7dde242"
---

# Installing sharpfuzz

> AFL-based fuzz testing for .NET.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sharpfuzz) (version `v2.2.0.r9.g7dde242`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S sharpfuzz    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/Metalnem/sharpfuzz) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `dotnet-sdk`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `afl++`
  - `dotnet-runtime`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[sharpfuzz]] · [[BlackArch Tools]]

