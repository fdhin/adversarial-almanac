---
tool: "rusthound-ce"
build-system: "go"
upstream: "https://github.com/g0h4n/RustHound-CE"
recipe: "blackarch"
nix-package: "rusthound-ce"
aur-package: "rusthound-ce"
source: "https://github.com/g0h4n/RustHound-CE"
license: "MIT"
blackarch-pkgver: "v2.5.30.r0.gbaa7285"
---

# Installing rusthound-ce

> Active Directory data collector for BloodHound community edition (v5).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rusthound-ce) (version `v2.5.30.r0.gbaa7285`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S rusthound-ce    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/g0h4n/RustHound-CE/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/g0h4n/RustHound-CE) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `cargo`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `clang`
  - `krb5`

## Exact build steps from the recipe

```bash
cd $pkgname

  cargo build --release --locked
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[rusthound-ce]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#rusthound-ce    # ad-hoc run, no install
nix profile install nixpkgs#rusthound-ce   # or persist it
```
