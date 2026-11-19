---
tool: "fragrouter"
build-system: "make"
upstream: "http://www.anzen.com/research/nidsbench/fragrouter.html"
recipe: "kali-debian"
version: "1.7-3kali6"
---

# Installing fragrouter

> IDS evasion toolkit Fragrouter is a network intrusion detection evasion toolkit.

Recipe source of truth: Kali's [`debian/` packaging](https://gitlab.com/kalilinux/packages/fragrouter) (version `1.7-3kali6`).

## Kali / Debian-based

```bash
sudo apt install fragrouter
```

## Arch / BlackArch

Not packaged. Check the AUR (`paru -Ss fragrouter`); otherwise build from source below.

## Any other Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://www.anzen.com/research/nidsbench/fragrouter.html) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from debian/control)

- Build (Debian names): `bison`, `flex`, `libdumbnet-dev`, `libevent-dev`, `libpcap0.8-dev`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@ 
override_dh_autoreconf:
override_dh_auto_install:
```

Backlinks: [[recon]] · [[fragrouter]] · [[Kali Gap Analysis]]
