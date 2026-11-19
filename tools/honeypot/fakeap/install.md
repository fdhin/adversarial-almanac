---
tool: "fakeap"
build-system: "perl"
upstream: "http://www.blackalchemy.to/project/fakeap/"
recipe: "blackarch"
source: "http://www.blackalchemy.to/project/fakeap/"
license: "GPL"
blackarch-pkgver: "0.3.2"
---

# Installing fakeap

> Black Alchemy's Fake AP generates thousands of counterfeit 802.11b access points. Hide in plain sight amongst Fake AP's cacophony of beacon frames.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/fakeap) (version `0.3.2`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S fakeap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://www.blackalchemy.to/project/fakeap/) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[honeypot]] · [[fakeap]] · [[BlackArch Tools]]

