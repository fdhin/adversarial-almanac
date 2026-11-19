---
tool: "injectus"
build-system: "scripts"
upstream: "https://github.com/BountyStrike/Injectus"
recipe: "blackarch"
source: "https://github.com/BountyStrike/Injectus"
license: "custom:unknown"
blackarch-pkgver: "12.3c01fa0"
---

# Installing injectus

> CRLF and open redirect fuzzer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/injectus) (version `12.3c01fa0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S injectus    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/BountyStrike/Injectus.git
cd Injectus
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `aiodns`, `aiofiles`, `aiohttp`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[injectus]] · [[BlackArch Tools]]

