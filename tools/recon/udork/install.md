---
tool: "udork"
build-system: "perl"
upstream: "https://github.com/m3n0sd0n4ld/uDork"
recipe: "blackarch"
source: "https://github.com/m3n0sd0n4ld/uDork"
license: "custom:unknown"
blackarch-pkgver: "102.1a0aab0"
---

# Installing udork

> Bash script that uses advanced Google search techniques to obtain sensitive information in files or directories, find IoT devices, detect versions of web applications.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/udork) (version `102.1a0aab0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S udork    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

```bash
git clone https://github.com/m3n0sd0n4ld/uDork.git
cd uDork
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `curl`
  - `perl`
  - `perl-uri`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[udork]] · [[BlackArch Tools]]

