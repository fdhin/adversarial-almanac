---
tool: "smtp-vrfy"
build-system: "perl"
upstream: ""
recipe: "blackarch"
source: "https://dl.packetstormsecurity.net/UNIX/scanners/Vrfy.tar.gz"
license: "custom:unknown"
blackarch-pkgver: "1.0"
---

# Installing smtp-vrfy

> An SMTP Protocol Hacker.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/smtp-vrfy) (version `1.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S smtp-vrfy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

```bash
curl -fLO https://dl.packetstormsecurity.net/UNIX/scanners/Vrfy.tar.gz
tar xf Vrfy.tar.gz
cd Vrfy
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[smtp-vrfy]] · [[BlackArch Tools]]

