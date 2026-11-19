---
tool: "asp-audit"
build-system: "perl"
upstream: "http://seclists.org/basics/2006/Sep/128"
recipe: "blackarch"
source: "http://seclists.org/basics/2006/Sep/128"
license: "custom:unknown"
blackarch-pkgver: "2BETA"
---

# Installing asp-audit

> An ASP fingerprinting tool and vulnerability scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/asp-audit) (version `2BETA`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S asp-audit    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://seclists.org/basics/2006/Sep/128) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fingerprint]] · [[asp-audit]] · [[BlackArch Tools]]

