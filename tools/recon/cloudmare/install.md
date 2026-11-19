---
tool: "cloudmare"
build-system: "scripts"
upstream: "https://github.com/MrH0wl/Cloudmare"
recipe: "blackarch"
source: "https://github.com/MrH0wl/Cloudmare"
license: "GPL3"
blackarch-pkgver: "108.9c5a39f"
---

# Installing cloudmare

> A simple tool to find origin servers of websites protected by CloudFlare with a misconfiguration DNS.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cloudmare) (version `108.9c5a39f`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S cloudmare    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/MrH0wl/Cloudmare.git
cd Cloudmare
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `backoff`, `beautifulsoup4`, `certifi`, `lxml`, `pip`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `dos2unix`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[cloudmare]] · [[BlackArch Tools]]

