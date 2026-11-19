---
tool: "zsteg"
build-system: "ruby"
upstream: "https://github.com/zed-0xff/zsteg"
recipe: "blackarch"
nix-package: "zsteg"
aur-package: "zsteg"
source: "https://github.com/zed-0xff/zsteg"
license: "MIT"
blackarch-pkgver: "v0.2.14.r0.gb75b578"
---

# Installing zsteg

> Detect stegano-hidden data in PNG and BMP.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/zsteg) (version `v0.2.14.r0.gb75b578`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S zsteg    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `ruby`):**

**Source:** download the release from the [upstream site](https://github.com/zedxff/zsteg) (the recipe uses a versioned archive).

```bash
bundle install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `ruby`
  - `ruby-iostruct`
  - `ruby-prime`
  - `ruby-zpng`

## Exact build steps from the recipe

```bash
cd $pkgname

  gem build $pkgname.gemspec
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[stego]] · [[zsteg]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#zsteg    # ad-hoc run, no install
nix profile install nixpkgs#zsteg   # or persist it
```
