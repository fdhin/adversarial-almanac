---
tool: "lfi-scanner"
build-system: "perl"
upstream: "http://packetstormsecurity.com/files/102848/LFI-Scanner-4.0.html"
recipe: "blackarch"
source: "http://packetstormsecurity.com/files/102848/LFI-Scanner-4.0.html"
license: "custom:unknown"
blackarch-pkgver: "4.0"
---

# Installing lfi-scanner

> This is a simple perl script that enumerates local file inclusion attempts when given a specific target.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/lfi-scanner) (version `4.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S lfi-scanner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/102848/LFI-Scanner.0.html) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-http-message`
  - `perl-libwww`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[lfi-scanner]] · [[BlackArch Tools]]

