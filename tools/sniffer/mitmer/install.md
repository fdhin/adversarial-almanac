---
tool: "mitmer"
build-system: "scripts"
upstream: "https://github.com/husam212/MITMer"
recipe: "blackarch"
source: "https://github.com/husam212/MITMer"
license: "custom"
blackarch-pkgver: "22.b01c7fe"
---

# Installing mitmer

> A man-in-the-middle and phishing attack tool that steals the victim's credentials of some web services like Facebook.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mitmer) (version `22.b01c7fe`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S mitmer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/husam212/MITMer.git
cd MITMer
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-nfqueue, python2-scapy (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[sniffer]] · [[mitmer]] · [[BlackArch Tools]]

