---
tool: "cloudfail"
build-system: "scripts"
upstream: "https://github.com/m0rtem/CloudFail"
recipe: "blackarch"
source: "https://github.com/m0rtem/CloudFail"
license: "custom:unknown"
blackarch-pkgver: "86.c84219f"
---

# Installing cloudfail

> Utilize misconfigured DNS and old database records to find hidden IP's behind the CloudFlare network.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cloudfail) (version `86.c84219f`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S cloudfail    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/m0rtem/CloudFail.git
cd CloudFail
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `colorama`, `dnspython`, `pyopenssl`, `pysocks`, `requests`, `win_inet_pton`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[cloudfail]] · [[BlackArch Tools]]

