---
tool: "fakenetbios"
build-system: "make"
upstream: "https://github.com/mubix/FakeNetBIOS"
recipe: "blackarch"
source: "https://github.com/mubix/FakeNetBIOS"
license: "custom"
blackarch-pkgver: "7.b83701e"
provides: ["fakenetbiosdgm", "fakenetbiosns"]
---

# Installing fakenetbios

> A family of tools designed to simulate Windows hosts (NetBIOS) on a LAN.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/fakenetbios) (version `7.b83701e`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S fakenetbios    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

```bash
git clone https://github.com/mubix/FakeNetBIOS.git
cd FakeNetBIOS
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Exact build steps from the recipe

```bash
cd "$pkgname/Sources"

  make -f Makefile.linux
```

## Installed layout (per recipe)

- Commands: `/usr/bin/fakenetbiosdgm`, `/usr/bin/fakenetbiosns`

Backlinks: [[spoof]] · [[fakenetbios]] · [[BlackArch Tools]]

