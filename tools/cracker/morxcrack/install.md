---
tool: "morxcrack"
build-system: "perl"
upstream: "http://www.morxploit.com/morxcrack/"
recipe: "blackarch"
source: "http://www.morxploit.com/morxcrack/"
license: "custom:unknown"
blackarch-pkgver: "1.2"
---

# Installing morxcrack

> A cracking tool written in Perl to perform a dictionary-based attack on various hashing algorithm and CMS salted-passwords.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/morxcrack) (version `1.2`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S morxcrack    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://www.morxploit.com/morxcrack/) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-digest-perl-md5`
  - `perl-digest-sha`
  - `perl-io-socket`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[morxcrack]] · [[BlackArch Tools]]

