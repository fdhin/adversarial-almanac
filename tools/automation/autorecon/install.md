---
tool: "autorecon"
build-system: "scripts"
upstream: "https://github.com/Tib3rius/AutoRecon"
recipe: "blackarch"
source: "https://github.com/Tib3rius/AutoRecon"
license: "GPL3"
blackarch-pkgver: "297.e7e98f6"
---

# Installing autorecon

> A multi-threaded network reconnaissance tool which performs automated enumeration of services.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/autorecon) (version `297.e7e98f6`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S autorecon    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Tib3rius/AutoRecon.git
cd AutoRecon
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `appdirs`, `colorama`, `psutil`, `toml`, `unidecode`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `feroxbuster`
  - `impacket`
  - `nmap`
  - `oscanner`
  - `python`
  - `redis`
  - `tnscmd`
  - `wkhtmltopdf`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[autorecon]] · [[BlackArch Tools]]

