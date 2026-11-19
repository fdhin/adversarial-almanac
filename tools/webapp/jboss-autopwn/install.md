---
tool: "jboss-autopwn"
build-system: "scripts"
upstream: "https://github.com/SpiderLabs/jboss-autopwn"
recipe: "blackarch"
source: "https://github.com/SpiderLabs/jboss-autopwn.git"
license: "GPL-3.0-or-later"
blackarch-pkgver: "2.986085d"
kali-package: "jboss-autopwn"
kali-version: "0.1-1kali2"
---

# Installing jboss-autopwn

> A JBoss script for obtaining remote shell access.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/jboss-autopwn) (version `2.986085d`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S jboss-autopwn    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install jboss-autopwn    # Kali package version 0.1-1kali2
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/SpiderLabs/jboss-autopwn.git
cd jboss-autopwn
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `curl`
  - `metasploit`
  - `sbd`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[jboss-autopwn]] · [[BlackArch Tools]]

