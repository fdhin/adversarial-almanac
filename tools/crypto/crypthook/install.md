---
tool: "crypthook"
build-system: "make"
upstream: "https://github.com/chokepoint/CryptHook"
recipe: "blackarch"
source: "https://github.com/chokepoint/CryptHook"
license: "custom:unknown"
blackarch-pkgver: "18.690dcae"
---

# Installing crypthook

> TCP/UDP symmetric encryption tunnel wrapper.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/crypthook) (version `18.690dcae`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S crypthook    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

```bash
git clone https://github.com/chokepoint/CryptHook.git
cd CryptHook
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

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[crypthook]] · [[BlackArch Tools]]

