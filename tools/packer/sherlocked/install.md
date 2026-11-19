---
tool: "sherlocked"
build-system: "make"
upstream: "https://github.com/elfmaster/sherlocked"
recipe: "blackarch"
source: "https://github.com/elfmaster/sherlocked"
license: "custom:unknown"
blackarch-pkgver: "1.f190c2b"
provides: ["gen-shellcode", "gen_shellcode"]
---

# Installing sherlocked

> Universal script packer-- transforms any type of script into a protected ELF executable, encrypted with anti-debugging.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sherlocked) (version `1.f190c2b`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S sherlocked    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/elfmaster/sherlocked) (the recipe uses a versioned archive).

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

- Commands: `/usr/bin/gen-shellcode`, `/usr/bin/gen_shellcode`

Backlinks: [[packer]] · [[sherlocked]] · [[BlackArch Tools]]

