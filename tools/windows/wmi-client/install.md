---
tool: "wmi-client"
ebuild-package: "wmi-client"
build-system: "make"
upstream: "https://bitbucket.org/instarch/wmi-client/"
recipe: "archstrike-pkgbuild"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/wmi-client"
version: "1.3.14"
license: "GPL2"
---

# Installing wmi-client

> Linux WMI client

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/wmi-client) (version `1.3.14`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S wmi-client
```

The same PKGBUILD is usually on the AUR (`paru -S wmi-client`).

## Any other Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://bitbucket.org/instarch/wmi-client/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `gnutls`
  - `pam`
  - `python2`

## Exact build steps from the recipe

```bash
export ZENHOME="${pkgdir}/usr"
  cd "${srcdir}/wmi-${pkgver}"

  patch -Np0 -i ../gnutls.patch
  patch -Np0 -i ../python3.patch
  patch -p1 -i ../pyinclude.patch
  patch -p1 -i ../samba-pidl.patch

  grep '^#!/usr/bin/python$' * -Rl | xargs -I{} sed -i 's|#!/usr/bin/python|#!/usr/bin/python2|g' {}
  sed -i '/^pywmi-installed:/s/pywmi-build//' GNUmakefile

  make build
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[windows]] · [[wmi-client]] · [[ArchStrike Gap Analysis]]
