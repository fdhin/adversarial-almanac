---
tool: "notspikefile"
build-system: "make"
upstream: "http://packetstormsecurity.com/files/39627/notSPIKEfile.tgz.html"
recipe: "blackarch"
source: "https://dl.packetstormsecurity.net/fuzzer/notSPIKEfile.tgz"
license: "GPL"
blackarch-pkgver: "0.1"
---

# Installing notspikefile

> A Linux based file format fuzzing tool

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/notspikefile) (version `0.1`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S notspikefile    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

```bash
curl -fLO https://dl.packetstormsecurity.net/fuzzer/notSPIKEfile.tgz
tar xf notSPIKEfile.tgz
cd notSPIKEfile
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Exact build steps from the recipe

```bash
cd dist

  make -C libdisasm/src/arch/i386/libdisasm/

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[notspikefile]] · [[BlackArch Tools]]

