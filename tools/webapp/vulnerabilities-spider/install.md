---
tool: "vulnerabilities-spider"
build-system: "scripts"
upstream: "https://github.com/muhammad-bouabid/Vulnerabilities-spider"
recipe: "blackarch"
source: "https://github.com/muhammad-bouabid/Vulnerabilities-spider"
license: "custom:unknown"
blackarch-pkgver: "1.426e70f"
---

# Installing vulnerabilities-spider

> A tool to scan for web vulnerabilities.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/vulnerabilities-spider) (version `1.426e70f`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S vulnerabilities-spider    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/muhammad-bouabid/Vulnerabilities-spider.git
cd Vulnerabilities-spider
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `gobject`, `pymysql`, `requests`, `selenium`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `gtk3` — Debian libgtk-3-dev / Fedora gtk3-devel
  - `pygtk`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[vulnerabilities-spider]] · [[BlackArch Tools]]

