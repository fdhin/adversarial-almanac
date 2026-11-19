---
tool: "domi-owned"
build-system: "scripts"
upstream: "https://github.com/coldfusion39/domi-owned"
recipe: "blackarch"
source: "https://github.com/coldfusion39/domi-owned"
license: "MIT"
blackarch-pkgver: "41.583d0a5"
---

# Installing domi-owned

> A tool used for compromising IBM/Lotus Domino servers.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/domi-owned) (version `41.583d0a5`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S domi-owned    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/coldfusion39/domi-owned) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `aiohttp`, `beautifulsoup4`, `fake-useragent`, `inflect`, `requests`, `simple-requests`, `tabulate`, `tqdm`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[domi-owned]] · [[BlackArch Tools]]

