---
tool: "id-entify"
build-system: "scripts"
upstream: "https://github.com/BillyV4/ID-entify"
recipe: "blackarch"
source: "https://github.com/BillyV4/ID-entify"
license: "custom:unknown"
blackarch-pkgver: "34.dd064a5"
---

# Installing id-entify

> Search for information related to a domain: Emails - IP addresses - Domains - Information on WEB technology - Type of Firewall - NS and MX records.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/id-entify) (version `34.dd064a5`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S id-entify    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/BillyV4/ID-entify.git
cd ID-entify
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `bind-tools`
  - `dnsenum`
  - `dnsrecon`
  - `fierce`
  - `nmap`
  - `python2`
  - `theharvester`
  - `tmux`
  - `wafw00f`
  - `whatweb`
  - `whois`
- ⚠️ Python 2 only deps: python2-colorama (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[id-entify]] · [[BlackArch Tools]]

