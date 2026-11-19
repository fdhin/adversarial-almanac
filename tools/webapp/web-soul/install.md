---
tool: "web-soul"
build-system: "perl"
upstream: "http://packetstormsecurity.com/files/122064/Web-Soul-2-Scanner.html"
recipe: "blackarch"
source: "http://packetstormsecurity.com/files/122064/Web-Soul-2-Scanner.html"
license: "custom:unknown"
blackarch-pkgver: "2"
---

# Installing web-soul

> A plugin based scanner for attacking and data mining web sites written in Perl.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/web-soul) (version `2`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S web-soul    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/122064/Web-Soul-Scanner.html) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[web-soul]] · [[BlackArch Tools]]

