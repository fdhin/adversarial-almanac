---
tool: "rapidscan"
build-system: "scripts"
upstream: "https://github.com/skavngr/rapidscan"
recipe: "blackarch"
source: "https://github.com/skavngr/rapidscan"
license: "GPL2"
blackarch-pkgver: "221.296a20b"
---

# Installing rapidscan

> The Multi-Tool Web Vulnerability Scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rapidscan) (version `221.296a20b`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S rapidscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/skavngr/rapidscan) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bind-tools`
  - `davtest`
  - `dirb`
  - `dmitry`
  - `dnsenum`
  - `dnsmap`
  - `dnsrecon`
  - `dnswalk`
  - `fierce`
  - `golismero`
  - `lbd`
  - `nikto`
  - `nmap`
  - `python`
  - `sslyze`
  - `theharvester`
  - `uniscan`
  - `wafw00f`
  - `wapiti`
  - `wget`
  - `whatweb`
  - `whois`
  - `xsser`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[rapidscan]] · [[BlackArch Tools]]

