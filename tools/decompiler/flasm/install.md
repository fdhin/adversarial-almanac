---
tool: "flasm"
build-system: "make"
upstream: "http://www.nowrap.de/flasm.html"
recipe: "blackarch"
nix-package: "flasm"
aur-package: "flasm"
source: "http://www.nowrap.de/download/flasm16src.zip"
license: "custom"
blackarch-pkgver: "1.62"
---

# Installing flasm

> Disassembler tool for SWF bytecode.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/flasm) (version `1.62`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S flasm    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

```bash
curl -fLO http://www.nowrap.de/download/flasm16src.zip
tar xf flasm16src.zip
cd flasm16src
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `gperf`
  - `zlib` — Debian zlib1g-dev / Fedora zlib-devel

## Exact build steps from the recipe

```bash
make CFLAGS="$CFLAGS -fcommon"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[decompiler]] · [[flasm]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#flasm    # ad-hoc run, no install
nix profile install nixpkgs#flasm   # or persist it
```
