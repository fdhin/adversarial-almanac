---
tool: "vanguard"
build-system: "perl"
upstream: "http://packetstormsecurity.com/files/110603/Vanguard-Pentesting-Scanner.html"
recipe: "blackarch"
source: "https://dl.packetstormsecurity.net/UNIX/scanners/vanguard-public.tgz"
license: "custom:unknown"
blackarch-pkgver: "0.1"
---

# Installing vanguard

> A comprehensive web penetration testing tool written in Perl thatidentifies vulnerabilities in web applications.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/vanguard) (version `0.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S vanguard    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

```bash
curl -fLO https://dl.packetstormsecurity.net/UNIX/scanners/vanguard-public.tgz
tar xf vanguard-public.tgz
cd vanguard-public
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-clone`
  - `perl-data-dumper`
  - `perl-io-socket`
  - `perl-libwww`
  - `perl-yaml`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[vanguard]] · [[BlackArch Tools]]

