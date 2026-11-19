---
tool: "ssh-privkey-crack"
build-system: "other"
upstream: "https://code.google.com/p/lusas/"
recipe: "blackarch"
source: "https://code.google.com/p/lusas/"
license: "custom:unknown"
blackarch-pkgver: "0.4"
---

# Installing ssh-privkey-crack

> A SSH private key cracker.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ssh-privkey-crack) (version `0.4`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S ssh-privkey-crack    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://code.google.com/p/lusas/) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `subversion`

## Exact build steps from the recipe

```bash
cd "$pkgname/ssh-key-crack"

  gcc -Wall $CFLAGS -lssl -lcrypto -o ssh-privkey-crack ssh-privkey-crack.c
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[ssh-privkey-crack]] · [[BlackArch Tools]]

