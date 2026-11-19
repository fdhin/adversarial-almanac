---
tool: "pupy"
build-system: "scripts"
upstream: "https://github.com/n1nj4sec/pupy"
recipe: "blackarch"
source: "https://github.com/n1nj4sec/pupy"
license: "custom:unknown"
blackarch-pkgver: "2988.4b78dc58"
provides: ["pupygen", "pupysh"]
---

# Installing pupy

> Opensource, cross-platform (Windows, Linux, OSX, Android) remote administration and post-exploitation tool mainly written in python.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pupy) (version `2988.4b78dc58`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S pupy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/n1nj4sec/pupy) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `impacket`
  - `libffi`
  - `python`
  - `tcpdump`
  - `wget`
- ⚠️ Python 2 only deps: python2-cerberus, python2-colorama, python2-dateparser, python2-defusedxml, python2-dnslib, python2-ecdsa, python2-filemagic, python2-flake8, python2-flake8-per-file-ignores, python2-fuse, python2-hexdump, python2-http-parser, python2-keyboard, python2-logutils, python2-m2crypto, python2-msgpack, python2-mss, python2-netaddr, python2-netifaces, python2-paramiko, python2-pefile, python2-poster, python2-psutil, python2-puttykeys, python2-pyaes, python2-pyaml, python2-pycryptodome, python2-pyelftools, python2-pygments, python2-pylzma, python2-pyopenssl, python2-pypykatz, python2-pyuv, python2-regex, python2-requests, python2-rpyc, python2-rsa, python2-scandir, python2-scapy, python2-secretstorage, python2-tinyec, python2-tornado, python2-tzlocal, python2-win_inet_pton, python2-xattr (legacy; needs python2)

## Installed layout (per recipe)

- Commands: `/usr/bin/pupygen`, `/usr/bin/pupysh`

Backlinks: [[automation]] · [[pupy]] · [[BlackArch Tools]]

