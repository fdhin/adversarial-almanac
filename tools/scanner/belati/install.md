---
tool: "belati"
build-system: "scripts"
upstream: "https://github.com/aancw/Belati"
recipe: "blackarch"
source: "https://github.com/aancw/Belati"
license: "GPL2"
blackarch-pkgver: "72.49577a1"
---

# Installing belati

> The Traditional Swiss Army Knife for OSINT.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/belati) (version `72.49577a1`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S belati    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/aancw/Belati.git
cd Belati
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-api-dnsdumpster, python2-argparse, python2-beautifulsoup4, python2-cmd2, python2-colorama, python2-django, python2-dnsknife, python2-dnspython, python2-fake-useragent, python2-future, python2-futures, python2-pip, python2-pyexifinfo, python2-python-geoip, python2-python-geoip-geolite2, python2-python-wappalyzer, python2-python-whois, python2-requests, python2-tabulate, python2-termcolor, python2-texttable, python2-tldextract, python2-tqdm, python2-validators (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[belati]] · [[BlackArch Tools]]

