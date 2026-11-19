---
tool: "shortfuzzy"
build-system: "perl"
upstream: "http://packetstormsecurity.com/files/104872/Short-Fuzzy-Rat-Scanner.html"
recipe: "blackarch"
source: "http://packetstormsecurity.com/files/104872/Short-Fuzzy-Rat-Scanner.html"
license: "custom:unknown"
blackarch-pkgver: "0.1"
---

# Installing shortfuzzy

> A web fuzzing script written in perl.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/shortfuzzy) (version `0.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S shortfuzzy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/104872/Short-Fuzzy-Rat-Scanner.html) (the recipe uses a versioned archive).

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

Backlinks: [[webapp]] · [[shortfuzzy]] · [[BlackArch Tools]]

