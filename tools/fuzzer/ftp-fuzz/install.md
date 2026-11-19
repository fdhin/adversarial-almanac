---
tool: "ftp-fuzz"
build-system: "scripts"
upstream: "http://nullsecurity.net/tools/fuzzer.html"
recipe: "blackarch"
source: "http://nullsecurity.net/tools/fuzzer.html"
license: "custom"
blackarch-pkgver: "1337"
---

# Installing ftp-fuzz

> The master of all master fuzzing scripts specifically targeted towards FTP server software.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ftp-fuzz) (version `1337`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S ftp-fuzz    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://nullsecurity.net/tools/fuzzer.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[ftp-fuzz]] · [[BlackArch Tools]]

