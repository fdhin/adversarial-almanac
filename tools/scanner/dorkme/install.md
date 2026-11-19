---
tool: "dorkme"
build-system: "scripts"
upstream: "https://github.com/blueudp/DorkMe"
recipe: "blackarch"
source: "https://github.com/blueudp/DorkMe"
license: "GPL3"
blackarch-pkgver: "57.0a7017a"
---

# Installing dorkme

> Tool designed with the purpose of making easier the searching of vulnerabilities with Google Dorks, such as SQL Injection vulnerabilities.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dorkme) (version `57.0a7017a`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S dorkme    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/blueudp/DorkMe.git
cd DorkMe
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `google-search-api`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[dorkme]] · [[BlackArch Tools]]

