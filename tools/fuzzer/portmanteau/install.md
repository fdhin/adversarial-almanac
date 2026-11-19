---
tool: "portmanteau"
build-system: "make"
upstream: "https://packetstormsecurity.com/files/134230/Portmanteau-Unix-Driver-IOCTL-Security-Tool.html"
recipe: "blackarch"
source: "https://packetstormsecurity.com/files/134230/Portmanteau-Unix-Driver-IOCTL-Security-Tool.html"
license: "custom:unknown"
blackarch-pkgver: "1.0"
---

# Installing portmanteau

> An experimental unix driver IOCTL security tool that is useful for fuzzing and discovering device driver attack surface.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/portmanteau) (version `1.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S portmanteau    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://packetstormsecurity.com/files/134230/Portmanteau-Unix-Driver-IOCTL-Security-Tool.html) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `sqlite` — Debian libsqlite3-dev / Fedora sqlite-devel

## Exact build steps from the recipe

```bash
cd "$pkgname-release"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[portmanteau]] · [[BlackArch Tools]]

