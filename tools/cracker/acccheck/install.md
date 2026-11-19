---
tool: "acccheck"
build-system: "perl"
upstream: "http://labs.portcullis.co.uk/tools/acccheck/"
recipe: "blackarch"
source: "http://labs.portcullis.co.uk/tools/acccheck/"
license: "GPL2"
blackarch-pkgver: "0.2.1"
---

# Installing acccheck

> A password dictionary attack tool that targets windows authentication via the SMB protocol.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/acccheck) (version `0.2.1`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S acccheck    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://labs.portcullis.co.uk/tools/acccheck/) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `smbclient`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[acccheck]] · [[BlackArch Tools]]

