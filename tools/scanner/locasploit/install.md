---
tool: "locasploit"
build-system: "scripts"
upstream: "https://github.com/lightfaith/locasploit"
recipe: "blackarch"
source: "https://github.com/lightfaith/locasploit"
license: "GPL2"
blackarch-pkgver: "117.fa48151"
---

# Installing locasploit

> Local enumeration and exploitation framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/locasploit) (version `117.fa48151`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S locasploit    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/lightfaith/locasploit) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `binwalk`
  - `python3`
- ⚠️ Python 2 only deps: python2-scapy (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[locasploit]] · [[BlackArch Tools]]

