---
tool: "rats"
build-system: "autotools"
upstream: "https://github.com/andrew-d/rough-auditing-tool-for-security"
recipe: "blackarch"
aur-package: "rats"
source: "https://github.com/andrew-d/rough-auditing-tool-for-security"
license: "GPL2"
blackarch-pkgver: "6.4ba54ce"
---

# Installing rats

> A rough auditing tool for security in source code files.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rats) (version `6.4ba54ce`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S rats    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

```bash
git clone https://github.com/andrew-d/rough-auditing-tool-for-security.git
cd rough-auditing-tool-for-security
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `expat`

## Exact build steps from the recipe

```bash
cd $pkgname

  ./configure --prefix=/usr

  make SHAREDIR="/usr/share/$pkgname"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[code-audit]] · [[rats]] · [[BlackArch Tools]]

