---
tool: "trixd00r"
build-system: "make"
upstream: "http://nullsecurity.net/tools/backdoor.html"
recipe: "blackarch"
source: "http://nullsecurity.net/tools/backdoor.html"
license: "custom:unknown"
blackarch-pkgver: "0.0.1"
---

# Installing trixd00r

> An advanced and invisible userland backdoor based on TCP/IP for UNIX systems.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/trixd00r) (version `0.0.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S trixd00r    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://nullsecurity.net/tools/backdoor.html) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  # We can't build the solaris client.
  # The client should be built for Linux so the 'linux' system must be last in the list.
  for sys in freebsd netbsd openbsd macos aix hpux irix linux ; do
    rm -rf $sys
    make MAKE=make $sys
    mkdir $sys
    mv trixd00rd trixd00rd-$sys
  done
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[backdoor]] · [[trixd00r]] · [[BlackArch Tools]]

