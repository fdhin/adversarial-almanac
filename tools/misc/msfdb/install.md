---
tool: "msfdb"
build-system: "scripts"
upstream: "https://github.com/BlackArch/msfdb"
recipe: "blackarch"
source: "https://github.com/BlackArch/msfdb"
license: "custom:unknown"
blackarch-pkgver: "16.09c603b"
---

# Installing msfdb

> Manage the metasploit framework database.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/msfdb) (version `16.09c603b`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S msfdb    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/BlackArch/msfdb) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `lsof`
  - `metasploit`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `postgresql`
  - `postgresql-libs` — Debian libpq-dev / Fedora libpq-devel
  - `sed`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/bash-completion`

Backlinks: [[misc]] · [[msfdb]] · [[BlackArch Tools]]

