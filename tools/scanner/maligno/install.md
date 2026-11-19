---
tool: "maligno"
build-system: "scripts"
upstream: "http://www.encripto.no/tools/"
recipe: "blackarch"
source: "http://www.encripto.no/tools/"
license: "BSD"
blackarch-pkgver: "2.5"
provides: ["maligno-clientgen", "maligno-srv"]
---

# Installing maligno

> An open source penetration testing tool written in python, that serves Metasploit payloads. It generates shellcode with msfvenom and transmits it over HTTP or HTTPS.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/maligno) (version `2.5`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S maligno    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.encripto.no/tools/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `metasploit`
  - `python2`
- ⚠️ Python 2 only deps: python2-ipcalc, python2-lxml, python2-pycryptodome (legacy; needs python2)

## Installed layout (per recipe)

- Commands: `/usr/bin/maligno-clientgen`, `/usr/bin/maligno-srv`

Backlinks: [[scanner]] · [[maligno]] · [[BlackArch Tools]]

