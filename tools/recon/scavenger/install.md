---
tool: "scavenger"
build-system: "scripts"
upstream: "https://github.com/rndinfosecguy/Scavenger"
recipe: "blackarch"
aur-package: "scavenger"
source: "https://github.com/rndinfosecguy/Scavenger"
license: "Apache-2.0"
blackarch-pkgver: "118.8f5d432"
---

# Installing scavenger

> Crawler (Bot) searching for credential leaks on different paste sites.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/scavenger) (version `118.8f5d432`, license Apache-2.0).

## Arch / BlackArch

```bash
sudo pacman -S scavenger    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/rndinfosecguy/Scavenger.git
cd Scavenger
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `colorama`, `httplib2`, `requests`, `tweepy`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`
  - `tmux`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[scavenger]] · [[BlackArch Tools]]

