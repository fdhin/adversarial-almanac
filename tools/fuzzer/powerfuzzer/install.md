---
tool: "powerfuzzer"
build-system: "scripts"
upstream: "http://www.powerfuzzer.com"
recipe: "blackarch"
source: "http://www.powerfuzzer.com"
license: "GPL"
blackarch-pkgver: "1_beta"
---

# Installing powerfuzzer

> Powerfuzzer is a highly automated web fuzzer based on many other Open Source fuzzers available (incl. cfuzzer, fuzzled, fuzzer.pl, jbrofuzz, webscarab, wapiti, Socket Fuzzer). It can detect XSS, Injections (SQL, LDAP, commands, code, XPATH) and others.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/powerfuzzer) (version `1_beta`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S powerfuzzer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.powerfuzzer.com) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
  - `wxpython`
- ⚠️ Python 2 only deps: python2-utidylib (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[powerfuzzer]] · [[BlackArch Tools]]

