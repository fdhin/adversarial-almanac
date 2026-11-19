---
tool: "revsh"
build-system: "make"
upstream: "https://github.com/emptymonkey/revsh/"
recipe: "blackarch"
source: "https://github.com/openssl/openssl.git"
license: "MIT"
blackarch-pkgver: "267.36a9a90"
provides: ["revsh-in_the_key_of_c"]
---

# Installing revsh

> A reverse shell with terminal support, data tunneling, and advanced pivoting capabilities.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/revsh) (version `267.36a9a90`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S revsh    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

```bash
git clone https://github.com/openssl/openssl.git
cd openssl
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `openssl` — Debian libssl-dev / Fedora openssl-devel

## Exact build steps from the recipe

```bash
cd openssl

  ./config no-shared -static
  make

  cd "$srcdir/$pkgname"

  make
```

## Installed layout (per recipe)

- Commands: `/usr/bin/revsh-in_the_key_of_c`
- Data: `/usr/share/man`

Backlinks: [[backdoor]] · [[revsh]] · [[BlackArch Tools]]

