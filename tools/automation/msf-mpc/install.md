---
tool: "msf-mpc"
build-system: "scripts"
upstream: "https://github.com/g0tmi1k/mpc"
recipe: "blackarch"
source: "https://github.com/g0tmi1k/mpc"
license: "MIT"
blackarch-pkgver: "35.8007ef2"
---

# Installing msf-mpc

> Msfvenom payload creator.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/msf-mpc) (version `35.8007ef2`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S msf-mpc    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/g0tmi1k/mpc.git
cd mpc
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `metasploit`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[msf-mpc]] · [[BlackArch Tools]]

