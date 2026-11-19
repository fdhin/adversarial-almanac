---
tool: "drozer"
build-system: "scripts"
upstream: "https://github.com/mwrlabs/drozer"
recipe: "blackarch"
source: "https://github.com/mwrlabs/drozer"
license: "BSD"
blackarch-pkgver: "2.4.4"
provides: ["drozer-complete", "drozer-repository"]
---

# Installing drozer

> A security testing framework for Android - Precompiled binary from official repository.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/drozer) (version `2.4.4`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S drozer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/mwrlabs/drozer) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `jre8-openjdk`
  - `protobuf`
  - `python2`
- ⚠️ Python 2 only deps: python2-protobuf, python2-pyopenssl, python2-service-identity, python2-twistedx (legacy; needs python2)

## Installed layout (per recipe)

- Commands: `/usr/bin/drozer-complete`, `/usr/bin/drozer-repository`

Backlinks: [[mobile]] · [[drozer]] · [[BlackArch Tools]]

