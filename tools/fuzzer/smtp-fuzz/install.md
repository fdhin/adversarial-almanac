---
tool: "smtp-fuzz"
build-system: "perl"
upstream: "https://no.ne/"
recipe: "blackarch"
source: "https://no.ne/"
license: "GPL"
blackarch-pkgver: "1.0"
---

# Installing smtp-fuzz

> Simple smtp fuzzer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/smtp-fuzz) (version `1.0`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S smtp-fuzz    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the none (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[smtp-fuzz]] · [[BlackArch Tools]]

