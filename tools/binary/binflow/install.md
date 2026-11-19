---
tool: "binflow"
build-system: "make"
upstream: "https://github.com/elfmaster/binflow"
recipe: "blackarch"
source: "https://github.com/elfmaster/binflow"
license: "custom:unknown"
blackarch-pkgver: "5.7fb02a9"
---

# Installing binflow

> POSIX function tracing. Much better and faster than ftrace.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/binflow) (version `5.7fb02a9`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S binflow    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/elfmaster/binflow) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `capstone`
  - `glibc`
  - `linux-headers`

## Exact build steps from the recipe

```bash
cd $pkgname

  mv capstone-2.1.2 capstone
  cd capstone
  make
  cd ..
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[binary]] · [[binflow]] · [[BlackArch Tools]]

