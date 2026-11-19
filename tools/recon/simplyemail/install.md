---
tool: "simplyemail"
build-system: "scripts"
upstream: "https://github.com/killswitch-GUI/SimplyEmail"
recipe: "blackarch"
source: "https://github.com/killswitch-GUI/SimplyEmail"
license: "GPL2"
blackarch-pkgver: "1.4.10.r7.6a42d37"
---

# Installing simplyemail

> Email recon made fast and easy, with a framework to build on CyberSyndicates.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/simplyemail) (version `1.4.10.r7.6a42d37`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S simplyemail    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/killswitch-GUI/SimplyEmail.git
cd SimplyEmail
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `antiword`
  - `odt2txt`
  - `python2`
  - `wget`
- ⚠️ Python 2 only deps: python2-beautifulsoup3, python2-beautifulsoup4, python2-configparser, python2-dnspython, python2-docx2txt, python2-fake-useragent, python2-lxml, python2-magic, python2-mechanize, python2-pdfminer, python2-requests, python2-xlsx2csv (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[simplyemail]] · [[BlackArch Tools]]

