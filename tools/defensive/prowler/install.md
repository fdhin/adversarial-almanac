---
tool: "prowler"
build-system: "scripts"
upstream: "https://github.com/alfresco/prowler"
recipe: "blackarch"
nix-package: "prowler"
source: "https://github.com/alfresco/prowler"
license: "Apache"
blackarch-pkgver: "9353.67aa941012"
---

# Installing prowler

> Tool for AWS security assessment, auditing and hardening.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/prowler) (version `9353.67aa941012`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S prowler    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/alfresco/prowler) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `aws-cli`
  - `bash`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[defensive]] · [[prowler]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#prowler    # ad-hoc run, no install
nix profile install nixpkgs#prowler   # or persist it
```
