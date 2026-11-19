---
tool: "lfi-fuzzploit"
build-system: "scripts"
upstream: "http://packetstormsecurity.com/files/106912/LFI-Fuzzploit-Tool-1.1.html"
recipe: "blackarch"
source: "http://packetstormsecurity.com/files/106912/LFI-Fuzzploit-Tool-1.1.html"
license: "GPL3"
blackarch-pkgver: "1.1"
---

# Installing lfi-fuzzploit

> A simple tool to help in the fuzzing for, finding, and exploiting of local file inclusion vulnerabilities in Linux-based PHP applications.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/lfi-fuzzploit) (version `1.1`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S lfi-fuzzploit    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/106912/LFI-Fuzzploit-Tool.1.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[lfi-fuzzploit]] · [[BlackArch Tools]]

