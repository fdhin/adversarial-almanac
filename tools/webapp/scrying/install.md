---
tool: "scrying"
build-system: "go"
upstream: "https://github.com/nccgroup/scrying"
recipe: "blackarch"
source: "https://github.com/nccgroup/scrying"
license: "GPL-3.0-or-later"
blackarch-pkgver: "v0.9.2.r0.g9d50ade"
---

# Installing scrying

> Collect RDP, web, and VNC screenshots smartly.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/scrying) (version `v0.9.2.r0.g9d50ade`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S scrying    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/nccgroup/scrying/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/nccgroup/scrying) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `cargo`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `chromium`
  - `webkit2gtk`

## Exact build steps from the recipe

```bash
cd $pkgname

  export CFLAGS+=' -ffat-lto-objects'
  cargo build --release --locked
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[scrying]] · [[BlackArch Tools]]

