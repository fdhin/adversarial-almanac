---
tool: "scannerl"
build-system: "scripts"
upstream: "https://github.com/kudelskisecurity/scannerl"
recipe: "blackarch"
aur-package: "scannerl"
source: "https://github.com/kudelskisecurity/scannerl"
license: "GPL3"
blackarch-pkgver: "76.8133065"
---

# Installing scannerl

> The modular distributed fingerprinting engine.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/scannerl) (version `76.8133065`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S scannerl    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/kudelskisecurity/scannerl) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `erlang`
  - `erlang-eldap`
  - `rebar`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fingerprint]] · [[scannerl]] · [[BlackArch Tools]]

