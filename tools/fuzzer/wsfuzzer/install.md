---
tool: "wsfuzzer"
build-system: "scripts"
upstream: "https://www.owasp.org/index.php/Category:OWASP_WSFuzzer_Project"
recipe: "blackarch"
source: "https://www.owasp.org/index.php/Category:OWASP_WSFuzzer_Project"
license: "LGPL"
blackarch-pkgver: "1.9.5"
---

# Installing wsfuzzer

> A Python tool written to automate SOAP pentesting of web services.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wsfuzzer) (version `1.9.5`, license LGPL).

## Arch / BlackArch

```bash
sudo pacman -S wsfuzzer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://www.owasp.org/index.php/Category:OWASP_WSFuzzer_Project) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-pyxml (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[wsfuzzer]] · [[BlackArch Tools]]

