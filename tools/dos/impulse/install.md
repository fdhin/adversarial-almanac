---
tool: "impulse"
build-system: "scripts"
upstream: "https://github.com/LimerBoy/Impulse"
recipe: "blackarch"
source: "https://github.com/LimerBoy/Impulse"
license: "GPL3"
blackarch-pkgver: "79.592a91b"
---

# Installing impulse

> Modern Denial-of-service ToolKit.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/impulse) (version `79.592a91b`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S impulse    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/LimerBoy/Impulse.git
cd Impulse
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `colorama`, `phonenumbers`, `requests`, `wget`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`
  - `scapy`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[dos]] · [[impulse]] · [[BlackArch Tools]]

