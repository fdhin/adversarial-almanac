---
tool: "sucrack"
build-system: "autotools"
upstream: "http://labs.portcullis.co.uk/application/sucrack"
recipe: "blackarch"
aur-package: "sucrack"
source: "http://labs.portcullis.co.uk/application/sucrack"
license: "custom"
blackarch-pkgver: "1.2.3"
kali-package: "sucrack"
kali-version: "1.2.3-8"
---

# Installing sucrack

> A multi-threaded Linux/UNIX tool for brute-force cracking local user accounts via su.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sucrack) (version `1.2.3`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S sucrack    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install sucrack    # Kali package version 1.2.3-8
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://labs.portcullis.co.uk/application/sucrack) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  unset LDFLAGS

  if [[ "$CARCH" == "i686" || "$CARCH" == "x86_64" ]];
  then
    ./configure --prefix=/usr --with-static-buffer
  else
    ./configure --build=arm --prefix=/usr --with-static-buffer
  fi

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[cracker]] · [[sucrack]] · [[BlackArch Tools]]

