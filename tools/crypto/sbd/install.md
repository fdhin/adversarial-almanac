---
tool: "sbd"
build-system: "make"
upstream: "http://www2.packetstormsecurity.org/cgi-bin/search/search.cgi?searchvalue=sbd"
recipe: "blackarch"
aur-package: "sbd"
source: "http://www2.packetstormsecurity.org/cgi-bin/search/search.cgi?searchvalue=sbd"
license: "GPL"
blackarch-pkgver: "1.36"
kali-package: "sbd"
kali-version: "1.37-1kali6"
---

# Installing sbd

> Netcat-clone, portable, offers strong encryption - features AES-CBC + HMAC-SHA1 encryption, program execution (-e), choosing source port, continuous reconnection with delay + more

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sbd) (version `1.36`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S sbd    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install sbd    # Kali package version 1.37-1kali6
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://www2.packetstormsecurity.org/cgi-bin/search/search.cgi?searchvalue=sbd) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  make unix
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[sbd]] · [[BlackArch Tools]]

