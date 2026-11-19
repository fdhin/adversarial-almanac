---
tool: "phishingkithunter"
build-system: "scripts"
upstream: "https://github.com/t4d/PhishingKitHunter"
recipe: "blackarch"
source: "https://github.com/t4d/PhishingKitHunter"
license: "AGPL3"
blackarch-pkgver: "20.ac9bd1e"
---

# Installing phishingkithunter

> Find phishing kits which use your brand/organization's files and image'.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/phishingkithunter) (version `20.ac9bd1e`, license AGPL3).

## Arch / BlackArch

```bash
sudo pacman -S phishingkithunter    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/t4d/PhishingKitHunter.git
cd PhishingKitHunter
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pysocks`, `python-whois`, `tqdm`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[phishingkithunter]] · [[BlackArch Tools]]

