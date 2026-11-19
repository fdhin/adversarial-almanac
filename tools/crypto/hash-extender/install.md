---
tool: "hash-extender"
build-system: "make"
upstream: "https://github.com/iagox86/hash_extender"
recipe: "blackarch"
source: "https://github.com/iagox86/hash_extender"
license: "BSD"
blackarch-pkgver: "161.f00b1a0"
provides: ["hash-extender-test"]
---

# Installing hash-extender

> A hash length extension attack tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hash-extender) (version `161.f00b1a0`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S hash-extender    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

```bash
git clone https://github.com/iagox86/hash_extender.git
cd hash_extender
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `openssl` — Debian libssl-dev / Fedora openssl-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- Commands: `/usr/bin/hash-extender-test`

Backlinks: [[crypto]] · [[hash-extender]] · [[BlackArch Tools]]

