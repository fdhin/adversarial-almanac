---
tool: "fdsploit"
build-system: "scripts"
upstream: "https://github.com/chrispetrou/FDsploit"
recipe: "blackarch"
source: "https://github.com/chrispetrou/FDsploit"
license: "GPL3"
blackarch-pkgver: "26.4522f53"
---

# Installing fdsploit

> A File Inclusion & Directory Traversal fuzzing, enumeration & exploitation tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/fdsploit) (version `26.4522f53`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S fdsploit    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/chrispetrou/FDsploit.git
cd FDsploit
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `colorama`, `fake-useragent`, `html5lib`, `requests`, `validators`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[fdsploit]] · [[BlackArch Tools]]

