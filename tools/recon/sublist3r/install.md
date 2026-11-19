---
tool: "sublist3r"
build-system: "scripts"
upstream: "https://github.com/aboul3la/Sublist3r"
recipe: "blackarch"
source: "https://github.com/aboul3la/Sublist3r"
license: "GPL2"
blackarch-pkgver: "138.729d649"
---

# Installing sublist3r

> A Fast subdomains enumeration tool for penetration testers.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sublist3r) (version `138.729d649`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S sublist3r    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/aboul3la/Sublist3r.git
cd Sublist3r
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `dnspython`, `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[sublist3r]] · [[BlackArch Tools]]

