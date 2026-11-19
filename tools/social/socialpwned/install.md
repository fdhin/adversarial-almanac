---
tool: "socialpwned"
build-system: "scripts"
upstream: "https://github.com/MrTuxx/SocialPwned"
recipe: "blackarch"
source: "https://github.com/MrTuxx/SocialPwned"
license: "GPL3"
blackarch-pkgver: "v2.0.1.r7.g333d759"
---

# Installing socialpwned

> OSINT tool that allows to get the emails, from a target, published in social networks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/socialpwned) (version `v2.0.1.r7.g333d759`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S socialpwned    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/MrTuxx/SocialPwned.git
cd SocialPwned
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifultable`, `bs4`, `chromedriver-autoinstaller`, `colorama`, `dateutil`, `geopy`, `httpx`, `imagehash`, `moviepy`, `pillow`, `pysocks`, `requests`, `requests-toolbelt`, `selenium`, `selenium-wire`, `termcolor`, `wayback`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`
  - `scrapy`
  - `twint`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[socialpwned]] · [[BlackArch Tools]]

