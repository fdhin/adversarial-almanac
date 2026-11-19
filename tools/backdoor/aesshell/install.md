---
tool: "aesshell"
build-system: "scripts"
upstream: "https://packetstormsecurity.com/files/132438/AESshell-0.7.html"
recipe: "blackarch"
source: "https://packetstormsecurity.com/files/132438/AESshell-0.7.html"
license: "custom:unknown"
blackarch-pkgver: "0.7"
---

# Installing aesshell

> A backconnect shell for Windows and Unix written in python and uses AES in CBC mode in conjunction with HMAC-SHA256 for secure transport.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/aesshell) (version `0.7`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S aesshell    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://packetstormsecurity.com/files/132438/AESshell.7.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-pycryptodome (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[backdoor]] · [[aesshell]] · [[BlackArch Tools]]

