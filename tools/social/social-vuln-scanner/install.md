---
tool: "social-vuln-scanner"
build-system: "scripts"
upstream: "https://github.com/Betawolf/social-vuln-scanner"
recipe: "blackarch"
source: "https://github.com/Betawolf/social-vuln-scanner"
license: "custom:unknown"
blackarch-pkgver: "11.91794c6"
---

# Installing social-vuln-scanner

> Gathers public information on companies to highlight social engineering risk.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/social-vuln-scanner) (version `11.91794c6`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S social-vuln-scanner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/Betawolf/social-vuln-scanner) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `editdistance`, `html2text`, `httplib2`, `oauth2`, `packaging`, `pillow`, `psutil`, `pyparsing`, `six`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `iotop`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[social-vuln-scanner]] · [[BlackArch Tools]]

