---
tool: "saruman"
build-system: "make"
upstream: "https://github.com/elfmaster/saruman"
recipe: "blackarch"
source: "https://github.com/elfmaster/saruman"
license: "custom:unknown"
blackarch-pkgver: "32.42316d2"
---

# Installing saruman

> ELF anti-forensics exec, for injecting full dynamic executables into process image (With thread injection).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/saruman) (version `32.42316d2`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S saruman    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/elfmaster/saruman) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[binary]] · [[saruman]] · [[BlackArch Tools]]

