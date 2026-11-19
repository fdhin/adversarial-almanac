---
tool: "o-saft"
build-system: "perl"
upstream: "https://www.owasp.org/index.php/O-Saft"
recipe: "blackarch"
source: "https://www.owasp.org/index.php/O-Saft"
license: "GPL2"
blackarch-pkgver: "7174.dbf462bc"
---

# Installing o-saft

> A tool to show informations about SSL certificate and tests the SSL connection according given list of ciphers and various SSL configurations.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/o-saft) (version `7174.dbf462bc`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S o-saft    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

```bash
git clone https://github.com/OWASP/O-Saft.git
cd O-Saft
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-io-socket-inet6`
  - `perl-io-socket-ssl`
  - `perl-net-ssleay`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[o-saft]] · [[BlackArch Tools]]

