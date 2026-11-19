---
tool: "melkor"
build-system: "make"
upstream: "http://packetstormsecurity.com/files/127924/Melkor-ELF-Fuzzer-1.0.html"
recipe: "blackarch"
source: "http://packetstormsecurity.com/files/127924/Melkor-ELF-Fuzzer-1.0.html"
license: "GPL2"
blackarch-pkgver: "1.0"
---

# Installing melkor

> An ELF fuzzer that mutates the existing data in an ELF sample given to create orcs (malformed ELFs), however, it does not change values randomly (dumb fuzzing), instead, it fuzzes certain metadata with semi-valid values through the use of fuzzing rules (knowledge base).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/melkor) (version `1.0`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S melkor    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/127924/Melkor-ELF-Fuzzer.0.html) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`

## Exact build steps from the recipe

```bash
cd "$pkgname-v$pkgver"

  make all
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[melkor]] · [[BlackArch Tools]]

