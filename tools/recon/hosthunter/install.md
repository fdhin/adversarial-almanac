---
tool: "hosthunter"
build-system: "scripts"
upstream: "https://github.com/SpiderLabs/HostHunter"
recipe: "blackarch"
source: "https://github.com/SpiderLabs/HostHunter"
license: "custom:unknown"
blackarch-pkgver: "158.553f1c7"
---

# Installing hosthunter

> A recon tool for discovering hostnames using OSINT techniques.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hosthunter) (version `158.553f1c7`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S hosthunter    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/SpiderLabs/HostHunter.git
cd HostHunter
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `fake-useragent`, `pyopenssl`, `requests`, `selenium`, `urllib3`, `validator-collection`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[hosthunter]] · [[BlackArch Tools]]

