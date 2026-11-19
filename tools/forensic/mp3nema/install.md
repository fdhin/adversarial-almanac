---
tool: "mp3nema"
build-system: "autotools"
upstream: "http://packetstormsecurity.com/files/76432/MP3nema-Forensic-Analysis-Tool.html"
recipe: "blackarch"
aur-package: "mp3nema"
source: "http://packetstormsecurity.com/files/76432/MP3nema-Forensic-Analysis-Tool.html"
license: "GPL3"
blackarch-pkgver: "0.4"
---

# Installing mp3nema

> A tool aimed at analyzing and capturing data that is hidden between frames in an MP3 file or stream, otherwise noted as "out of band" data.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mp3nema) (version `0.4`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S mp3nema    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/76432/MP3nema-Forensic-Analysis-Tool.html) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `glibc`

## Exact build steps from the recipe

```bash
cd "$pkgname-v${pkgver//./_}"

  ./configure --prefix=/usr

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[mp3nema]] · [[BlackArch Tools]]

