---
tool: "lfi-sploiter"
build-system: "scripts"
upstream: "http://packetstormsecurity.com/files/96056/Simple-Local-File-Inclusion-Exploiter-1.0.html"
recipe: "blackarch"
source: "http://packetstormsecurity.com/files/96056/Simple-Local-File-Inclusion-Exploiter-1.0.html"
license: "custom:unknown"
blackarch-pkgver: "1.0"
---

# Installing lfi-sploiter

> This tool helps you exploit LFI (Local File Inclusion) vulnerabilities. Post discovery, simply pass the affected URL and vulnerable parameter to this tool. You can also use this tool to scan a URL for LFI vulnerabilities.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/lfi-sploiter) (version `1.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S lfi-sploiter    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/96056/Simple-Local-File-Inclusion-Exploiter.0.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-httplib2 (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[lfi-sploiter]] · [[BlackArch Tools]]

