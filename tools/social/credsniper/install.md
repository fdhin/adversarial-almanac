---
tool: "credsniper"
build-system: "scripts"
upstream: "https://github.com/ustayready/CredSniper"
recipe: "blackarch"
source: "https://github.com/ustayready/CredSniper"
license: "Apache"
blackarch-pkgver: "21.f52461b"
---

# Installing credsniper

> Phishing framework written with the Python micro-framework Flask and Jinja2 templating which supports capturing 2FA tokens.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/credsniper) (version `21.f52461b`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S credsniper    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/ustayready/CredSniper.git
cd CredSniper
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `flask`, `mechanicalsoup`, `pyopenssl`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `gnupg`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[credsniper]] · [[BlackArch Tools]]

