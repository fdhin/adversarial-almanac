---
tool: "uniofuzz"
build-system: "scripts"
upstream: "http://nullsecurity.net/tools/fuzzer.html"
recipe: "blackarch"
source: "https://github.com/nullsecuritynet/tools/raw/main/fuzzer/uniofuzz/release/uniofuzz.py"
license: "custom:unknown"
blackarch-pkgver: "1337"
---

# Installing uniofuzz

> The universal fuzzing tool for browsers, web services, files, programs and network services/ports

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/uniofuzz) (version `1337`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S uniofuzz    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/nullsecuritynet/tools/raw/main/fuzzer/uniofuzz/release/uniofuzz.py
cd uniofuzz.py
# run scripts directly (see recipe for interpreter and layout)
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[uniofuzz]] · [[BlackArch Tools]]

