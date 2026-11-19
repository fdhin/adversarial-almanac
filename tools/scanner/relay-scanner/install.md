---
tool: "relay-scanner"
build-system: "perl"
upstream: "http://www.cirt.dk"
recipe: "blackarch"
source: "https://dl.packetstormsecurity.net/UNIX/mail/relayscanner.zip"
license: "custom:unkown"
blackarch-pkgver: "1.7"
---

# Installing relay-scanner

> An SMTP relay scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/relay-scanner) (version `1.7`, license custom:unkown).

## Arch / BlackArch

```bash
sudo pacman -S relay-scanner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

```bash
curl -fLO https://dl.packetstormsecurity.net/UNIX/mail/relayscanner.zip
tar xf relayscanner.zip
cd relayscanner
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `unzip`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[relay-scanner]] · [[BlackArch Tools]]

