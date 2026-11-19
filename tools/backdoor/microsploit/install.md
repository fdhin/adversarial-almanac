---
tool: "microsploit"
build-system: "scripts"
upstream: "https://github.com/Screetsec/Microsploit"
recipe: "blackarch"
source: "https://github.com/Screetsec/Microsploit"
license: "GPL3"
blackarch-pkgver: "9.441e132"
---

# Installing microsploit

> Fast and easy create backdoor office exploitation using module metasploit packet, Microsoft Office, Open Office, Macro attack, Buffer Overflow.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/microsploit) (version `9.441e132`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S microsploit    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Screetsec/Microsploit.git
cd Microsploit
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[backdoor]] · [[microsploit]] · [[BlackArch Tools]]

