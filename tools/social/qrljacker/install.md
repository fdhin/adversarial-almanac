---
tool: "qrljacker"
build-system: "scripts"
upstream: "https://github.com/OWASP/QRLJacking/tree/master/QRLJacker"
recipe: "blackarch"
source: "https://github.com/OWASP/QRLJacking/tree/master/QRLJacker"
license: "GPL"
blackarch-pkgver: "222.defd5e3"
---

# Installing qrljacker

> QRLJacker is a highly customizable exploitation framework to demonstrate "QRLJacking Attack Vector".

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/qrljacker) (version `222.defd5e3`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S qrljacker    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/OWASP/QRLJacking.git
cd QRLJacking
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `jinja`, `pathlib2`, `pillow`, `selenium`, `terminaltables`, `user-agent`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[qrljacker]] · [[BlackArch Tools]]

