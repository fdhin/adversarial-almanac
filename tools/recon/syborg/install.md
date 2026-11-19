---
tool: "syborg"
build-system: "scripts"
upstream: "https://github.com/MilindPurswani/Syborg"
recipe: "blackarch"
source: "https://github.com/MilindPurswani/Syborg"
license: "custom:unknown"
blackarch-pkgver: "36.5cd010b"
---

# Installing syborg

> Recursive DNS Subdomain Enumerator with dead-end avoidance system.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/syborg) (version `36.5cd010b`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S syborg    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/MilindPurswani/Syborg.git
cd Syborg
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `dnspython`, `filequeue`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[syborg]] · [[BlackArch Tools]]

