---
tool: "bluto"
build-system: "scripts"
upstream: "https://github.com/darryllane/Bluto"
recipe: "blackarch"
source: "https://github.com/darryllane/Bluto"
license: "GPL3"
blackarch-pkgver: "142.25cad7a"
---

# Installing bluto

> Recon, Subdomain Bruting, Zone Transfers.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bluto) (version `142.25cad7a`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S bluto    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/darryllane/Bluto
cd Bluto
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-beautifulsoup4, python2-dnspython2, python2-docopt, python2-lxml, python2-oletools, python2-pdfminer, python2-python2whois, python2-requests, python2-setuptools, python2-termcolor (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[bluto]] · [[BlackArch Tools]]

