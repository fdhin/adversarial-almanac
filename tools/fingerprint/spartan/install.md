---
tool: "spartan"
build-system: "scripts"
upstream: "https://github.com/sensepost/SPartan"
recipe: "blackarch"
source: "https://github.com/sensepost/SPartan"
license: "custom:unknown"
blackarch-pkgver: "23.babdd7d"
---

# Installing spartan

> Frontpage and Sharepoint fingerprinting and attack tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/spartan) (version `23.babdd7d`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S spartan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/sensepost/SPartan.git
cd SPartan
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-beautifulsoup4, python2-python-ntlm3, python2-requests, python2-requests-ntlm, python2-six (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fingerprint]] · [[spartan]] · [[BlackArch Tools]]

