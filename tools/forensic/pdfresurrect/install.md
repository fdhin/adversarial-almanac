---
tool: "pdfresurrect"
build-system: "autotools"
upstream: "http://packetstormsecurity.com/files/118459/PDFResurrect-PDF-Analyzer-0.12.html"
recipe: "blackarch"
aur-package: "pdfresurrect"
source: "http://packetstormsecurity.com/files/118459/PDFResurrect-PDF-Analyzer-0.12.html"
license: "GPL3"
blackarch-pkgver: "0.12"
---

# Installing pdfresurrect

> A tool aimed at analyzing PDF documents.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pdfresurrect) (version `0.12`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S pdfresurrect    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/118459/PDFResurrect-PDF-Analyzer.12.html) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `gzip`

## Exact build steps from the recipe

```bash
cd "$pkgname-v${pkgver/./_}"

  ./configure --prefix=/usr

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[forensic]] · [[pdfresurrect]] · [[BlackArch Tools]]

