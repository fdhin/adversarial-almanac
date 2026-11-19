---
tool: "hexhttp"
build-system: "scripts"
upstream: "https://github.com/c0dejump/HExHTTP"
recipe: "blackarch"
source: "https://github.com/c0dejump/HExHTTP"
license: "MIT"
blackarch-pkgver: "v2.7.r0.g20df13b"
---

# Installing hexhttp

> Perform tests on HTTP headers and analyze the results to identify vulnerabilities and interesting behaviors.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hexhttp) (version `v2.7.r0.g20df13b`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S hexhttp    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/c0dejump/HExHTTP) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `notify_py`, `requests`, `urllib3`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`
  - `wafw00f`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[hexhttp]] · [[BlackArch Tools]]

