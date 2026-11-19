---
tool: "dirsearch"
build-system: "scripts"
upstream: "https://github.com/maurosoria/dirsearch"
recipe: "blackarch"
nix-package: "dirsearch"
aur-package: "dirsearch"
source: "https://github.com/maurosoria/dirsearch"
license: "GPL2"
blackarch-pkgver: "3033.b45e01bb"
---

# Installing dirsearch

> HTTP(S) directory/file brute forcer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dirsearch) (version `3033.b45e01bb`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S dirsearch    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/maurosoria/dirsearch) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `colorama`, `defusedcsv`, `defusedxml`, `httpx`, `httpx-ntlm`, `jinja`, `mysql-connector`, `ntlm-auth`, `psycopg`, `pyopenssl`, `pysocks`, `requests`, `requests-ntlm`, `requests-toolbelt`, `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[dirsearch]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#dirsearch    # ad-hoc run, no install
nix profile install nixpkgs#dirsearch   # or persist it
```
