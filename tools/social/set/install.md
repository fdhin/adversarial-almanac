---
tool: "set"
build-system: "scripts"
upstream: "https://github.com/trustedsec/social-engineer-toolkit/tags"
recipe: "blackarch"
source: "https://github.com/trustedsec/social-engineer-toolkit/tags"
license: "BSD"
blackarch-pkgver: "8.0.3"
provides: ["setoolkit"]
kali-package: "set"
kali-version: "8.1.3+git20260604-0kali1"
---

# Installing set

> Social-engineer toolkit. Aimed at penetration testing around Social-Engineering.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/set) (version `8.0.3`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S set    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install set    # Kali package version 8.1.3+git20260604-0kali1
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/trustedsec/social-engineer-toolkit/tags) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `pefile`, `pexpect`, `pillow`, `pycryptodome`, `qrcode`, `requests`
- **Build tools**: `git`, `java-environment`
- **System libraries** (Arch name — translate for your distro):
  - `impacket`
  - `python`

## Installed layout (per recipe)

- Commands: `/usr/bin/setoolkit`

Backlinks: [[social]] · [[set]] · [[BlackArch Tools]]

