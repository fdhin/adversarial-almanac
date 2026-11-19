---
tool: "server-status-pwn"
build-system: "scripts"
upstream: "https://github.com/mazen160/server-status_PWN"
recipe: "blackarch"
source: "https://github.com/mazen160/server-status_PWN"
license: "MIT"
blackarch-pkgver: "12.841d55d"
---

# Installing server-status-pwn

> A script that monitors and extracts requested URLs and clients connected to the service by exploiting publicly accessible Apache server-status instances.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/server-status-pwn) (version `12.841d55d`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S server-status-pwn    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/mazen160/server-status_PWN.git
cd server-status_PWN
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[server-status-pwn]] · [[BlackArch Tools]]

