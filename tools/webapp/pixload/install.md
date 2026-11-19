---
tool: "pixload"
build-system: "perl"
upstream: "https://github.com/chinarulezzz/pixload"
recipe: "blackarch"
source: "https://github.com/chinarulezzz/pixload"
license: "WTFPL"
blackarch-pkgver: "87.a8f58a7"
---

# Installing pixload

> Image Payload Creating/Injecting tools.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pixload) (version `87.a8f58a7`, license WTFPL).

## Arch / BlackArch

```bash
sudo pacman -S pixload    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](https://github.com/chinarulezzz/pixload) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-gd`
  - `perl-image-exiftool`
  - `perl-string-crc32`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[pixload]] · [[BlackArch Tools]]

