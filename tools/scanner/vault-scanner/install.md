---
tool: "vault-scanner"
build-system: "scripts"
upstream: "https://github.com/abhisharma404/vault"
recipe: "blackarch"
source: "https://github.com/abhisharma404/vault"
license: "MIT"
blackarch-pkgver: "299.0303cf4"
---

# Installing vault-scanner

> Swiss army knife for hackers.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/vault-scanner) (version `299.0303cf4`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S vault-scanner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/abhisharma404/vault) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `htmlparser`, `ipwhois`, `pynput`, `python-nmap`, `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`
  - `scapy`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[vault-scanner]] · [[BlackArch Tools]]

