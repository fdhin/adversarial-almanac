---
tool: "letmefuckit-scanner"
build-system: "scripts"
upstream: "https://github.com/onthefrontline/LetMeFuckIt-Scanner"
recipe: "blackarch"
source: "https://github.com/onthefrontline/LetMeFuckIt-Scanner"
license: "custom:unknown"
blackarch-pkgver: "3.f3be22b"
provides: ["letmefuckit"]
---

# Installing letmefuckit-scanner

> Scanner and Exploit Magento.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/letmefuckit-scanner) (version `3.f3be22b`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S letmefuckit-scanner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/onthefrontline/LetMeFuckIt-Scanner.git
cd LetMeFuckIt-Scanner
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-pygoogle-simple, python2-setuptools (legacy; needs python2)

## Installed layout (per recipe)

- Commands: `/usr/bin/letmefuckit`

Backlinks: [[scanner]] · [[letmefuckit-scanner]] · [[BlackArch Tools]]

