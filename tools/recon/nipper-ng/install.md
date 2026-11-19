---
tool: "nipper-ng"
build-system: "other"
upstream: ""
recipe: "kali-debian"
version: "0.11.10-1kali3"
---

# Installing nipper-ng

> Device security configuration review tool Nipper-ng is the next generation of nippper, and will always remain free and open source. This software will be used to make observations about the security configurations of many different device types such as routers, firewalls, and switches of a network infrastructure. . This is a fork from nipper 0.11.10 release of the GNUv3 GPL code.

Recipe source of truth: Kali's [`debian/` packaging](https://gitlab.com/kalilinux/packages/nipper-ng) (version `0.11.10-1kali3`).

## Kali / Debian-based

```bash
sudo apt install nipper-ng
```

## Arch / BlackArch

Not packaged. Check the AUR (`paru -Ss nipper-ng`); otherwise build from source below.

## Any other Linux distro

**From source:**

**Source:** download the release from the the upstream site (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Build dependencies (from source .dsc)

- `debhelper-compat`
- Runtime deps: see [pkg.kali.org/pkg/nipper-ng](https://pkg.kali.org/pkg/nipper-ng)

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@
override_dh_auto_install:
```

Backlinks: [[recon]] · [[nipper-ng]] · [[Kali Gap Analysis]]
