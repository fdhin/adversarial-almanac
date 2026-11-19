---
tool: "ntlm-scanner"
build-system: "scripts"
upstream: "https://github.com/preempt/ntlm-scanner"
recipe: "blackarch"
source: "https://github.com/preempt/ntlm-scanner"
license: "MIT"
blackarch-pkgver: "6.4b29329"
---

# Installing ntlm-scanner

> A simple python tool based on Impacket that tests servers for various known NTLM vulnerabilities.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ntlm-scanner) (version `6.4b29329`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S ntlm-scanner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/preempt/ntlm-scanner) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-impacket, python2-setuptools (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[ntlm-scanner]] · [[BlackArch Tools]]

