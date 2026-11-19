---
tool: "git-wild-hunt"
build-system: "scripts"
upstream: "https://github.com/d1vious/git-wild-hunt"
recipe: "blackarch"
source: "https://github.com/d1vious/git-wild-hunt"
license: "Apache"
blackarch-pkgver: "16.6495672"
---

# Installing git-wild-hunt

> A tool to hunt for credentials in github wild AKA git*hunt.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/git-wild-hunt) (version `16.6495672`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S git-wild-hunt    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/d1vious/git-wild-hunt) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `certifi`, `chardet`, `idna`, `requests`, `tqdm`, `urllib3`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[git-wild-hunt]] · [[BlackArch Tools]]

