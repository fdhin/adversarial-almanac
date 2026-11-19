---
tool: "scrape-dns"
build-system: "scripts"
upstream: "https://github.com/304GEEK/Scrape-DNS"
recipe: "blackarch"
source: "https://github.com/304GEEK/Scrape-DNS"
license: "GPL2"
blackarch-pkgver: "58.3df392f"
---

# Installing scrape-dns

> Searches for interesting cached DNS entries.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/scrape-dns) (version `58.3df392f`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S scrape-dns    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/304GEEK/Scrape-DNS.git
cd Scrape-DNS
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `dnsutils`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/scrape-dns`

Backlinks: [[scanner]] · [[scrape-dns]] · [[BlackArch Tools]]

