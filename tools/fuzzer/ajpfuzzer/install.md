---
tool: "ajpfuzzer"
build-system: "scripts"
upstream: "https://github.com/doyensec/ajpfuzzer"
recipe: "blackarch"
source: "https://github.com/doyensec/ajpfuzzer"
license: "custom:unknown"
blackarch-pkgver: "0.7"
---

# Installing ajpfuzzer

> A command-line fuzzer for the Apache JServ Protocol (ajp13).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ajpfuzzer) (version `0.7`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S ajpfuzzer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/doyensec/ajpfuzzer) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`, `java-environment`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[ajpfuzzer]] · [[BlackArch Tools]]

