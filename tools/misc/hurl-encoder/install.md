---
tool: "hurl-encoder"
build-system: "perl"
upstream: "https://github.com/fnord0/hURL"
recipe: "blackarch"
source: "https://github.com/fnord0/hURL"
license: "custom:unknown"
blackarch-pkgver: "20.afca9c5"
---

# Installing hurl-encoder

> Hexadecimal & URL (en/de)coder.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hurl-encoder) (version `20.afca9c5`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S hurl-encoder    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

```bash
git clone https://github.com/fnord0/hURL.git
cd hURL
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-cgi`
  - `perl-digest-sha1`
  - `perl-mime-types`
  - `perl-uri`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[hurl-encoder]] · [[BlackArch Tools]]

