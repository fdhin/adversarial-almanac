---
tool: "morxbook"
build-system: "perl"
upstream: "http://www.morxploit.com/"
recipe: "blackarch"
source: "http://web.archive.org/web/20160908071425/http://morxploit.com:80/morxbook.pl"
license: "custom:unknown"
blackarch-pkgver: "1.0"
---

# Installing morxbook

> A password cracking tool written in perl to perform a dictionary-based attack on a specific Facebook user through HTTPS.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/morxbook) (version `1.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S morxbook    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://www.morxploit.com/) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-net-ssleay`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[morxbook]] · [[BlackArch Tools]]

