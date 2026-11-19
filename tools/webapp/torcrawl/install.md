---
tool: "torcrawl"
build-system: "scripts"
upstream: "https://github.com/MikeMeliz/TorCrawl.py"
recipe: "blackarch"
source: "https://github.com/MikeMeliz/TorCrawl.py"
license: "GPL-3.0-or-later"
blackarch-pkgver: "v1.35.r4.g2fb5d78"
---

# Installing torcrawl

> Crawl and extract (regular or onion) webpages through TOR network.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/torcrawl) (version `v1.35.r4.g2fb5d78`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S torcrawl    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/MikeMeliz/TorCrawl.py.git
cd TorCrawl.py
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `requests`, `socks`, `urllib3`, `yara-python-dex`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`
  - `tor`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[torcrawl]] · [[BlackArch Tools]]

