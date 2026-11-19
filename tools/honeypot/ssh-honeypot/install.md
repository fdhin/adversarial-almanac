---
tool: "ssh-honeypot"
build-system: "make"
upstream: "https://github.com/droberson/ssh-honeypot"
recipe: "blackarch"
source: "https://github.com/droberson/ssh-honeypot"
license: "GPL3"
blackarch-pkgver: "113.4bda71c"
---

# Installing ssh-honeypot

> Fake sshd that logs ip addresses, usernames, and passwords.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ssh-honeypot) (version `113.4bda71c`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S ssh-honeypot    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/droberson/ssh-honeypot) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`, `make`
- **System libraries** (Arch name — translate for your distro):
  - `clang`
  - `json-c`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `libssh`

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[honeypot]] · [[ssh-honeypot]] · [[BlackArch Tools]]

