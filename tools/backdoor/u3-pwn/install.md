---
tool: "u3-pwn"
build-system: "scripts"
upstream: "http://www.nullsecurity.net/tools/backdoor.html"
recipe: "blackarch"
source: "http://www.nullsecurity.net/tools/backdoor.html"
license: "GPL3"
blackarch-pkgver: "2.0"
---

# Installing u3-pwn

> A tool designed to automate injecting executables to Sandisk smart usb devices with default U3 software install.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/u3-pwn) (version `2.0`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S u3-pwn    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.nullsecurity.net/tools/backdoor.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `cdrkit`
  - `metasploit`
  - `python2`
  - `u3-tool`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[backdoor]] · [[u3-pwn]] · [[BlackArch Tools]]

