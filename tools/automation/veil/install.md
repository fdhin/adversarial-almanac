---
tool: "veil"
build-system: "scripts"
upstream: "https://github.com/Veil-Framework/Veil"
recipe: "blackarch"
source: "https://github.com/Veil-Framework/VeilDependencies.git"
license: "GPL2"
blackarch-pkgver: "297.d8acd4c"
kali-package: "veil"
kali-version: "3.1.14-0kali3"
---

# Installing veil

> A tool designed to generate metasploit payloads that bypass common anti-virus solutions.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/veil) (version `297.d8acd4c`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S veil    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install veil    # Kali package version 3.1.14-0kali3
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Veil-Framework/Veil.git
cd Veil
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `capstone`, `pefile`, `pip`, `pycryptodome`
- **Build tools**: `git`, `mono`
- **System libraries** (Arch name — translate for your distro):
  - `ca-certificates`
  - `capstone`
  - `metasploit`
  - `mingw-w64-binutils`
  - `mingw-w64-crt`
  - `mingw-w64-gcc`
  - `mingw-w64-headers`
  - `mingw-w64-winpthreads`
  - `mono-tools`
  - `pyinstaller`
  - `python`
  - `ruby`
  - `sudo`
  - `unzip`
  - `wget`
  - `wine`
  - `wine-mono`
  - `wine_gecko`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[veil]] · [[BlackArch Tools]]

