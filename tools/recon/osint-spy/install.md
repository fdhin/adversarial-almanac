---
tool: "osint-spy"
build-system: "scripts"
upstream: "https://github.com/SharadKumar97/OSINT-SPY"
recipe: "blackarch"
source: "https://github.com/SharadKumar97/OSINT-SPY"
license: "GPL-3.0-or-later"
blackarch-pkgver: "v2.0.r11.g03dcf48"
---

# Installing osint-spy

> Performs OSINT scan on email/domain/ip_address/organization.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/osint-spy) (version `v2.0.r11.g03dcf48`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S osint-spy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/SharadKumar97/OSINT-SPY.git
cd OSINT-SPY
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `clearbit`, `fullcontactpy`, `prospector`, `pythonwhois`, `requests`, `shodan`, `steganography`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`
  - `sslscan`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[osint-spy]] · [[BlackArch Tools]]

