---
tool: "empire"
build-system: "scripts"
upstream: "https://github.com/BC-SECURITY/Empire"
recipe: "blackarch"
source: "https://github.com/BC-SECURITY/Empire"
license: "BSD-3-Clause"
blackarch-pkgver: "v6.7.1.r0.gca5b774"
---

# Installing empire

> A PowerShell and Python post-exploitation agent.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/empire) (version `v6.7.1.r0.gca5b774`, license BSD-3-Clause).

## Arch / BlackArch

```bash
sudo pacman -S empire    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/BC-SECURITY/Empire.git
cd Empire
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pip`, `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `mariadb`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[empire]] · [[BlackArch Tools]]

