---
tool: "tftp-fuzz"
build-system: "scripts"
upstream: "http://nullsecurity.net/tools/fuzzer.html"
recipe: "blackarch"
source: "http://nullsecurity.net/tools/fuzzer.html"
license: "custom:unknown"
blackarch-pkgver: "1337"
---

# Installing tftp-fuzz

> Master TFTP fuzzing script as part of the ftools series of fuzzers.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/tftp-fuzz) (version `1337`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S tftp-fuzz    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://nullsecurity.net/tools/fuzzer.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[tftp-fuzz]] · [[BlackArch Tools]]

