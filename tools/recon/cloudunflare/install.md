---
tool: "cloudunflare"
build-system: "scripts"
upstream: "https://github.com/greycatz/CloudUnflare"
recipe: "blackarch"
source: "https://github.com/greycatz/CloudUnflare"
license: "custom:unknown"
blackarch-pkgver: "14.b91a8a7"
---

# Installing cloudunflare

> Reconnaissance Real IP address for Cloudflare Bypass.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cloudunflare) (version `14.b91a8a7`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S cloudunflare    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/greycatz/CloudUnflare.git
cd CloudUnflare
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `bind-tools`
  - `curl`
  - `whois`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[cloudunflare]] · [[BlackArch Tools]]

