---
tool: "md5bf"
build-system: "scripts"
upstream: "http://www.edge-security.com/edge-soft.php"
recipe: "pentoo-ebuild"
version: "0.1"
license: "GPL-2"
---

# Installing md5bf

> A tool to crack md5 hashes by bruteforce or dictionary attack

Recipe source of truth: Pentoo [`md5bf.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/app-crypt/md5bf) (version `0.1`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask md5bf
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss md5bf`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.edge-security.com/edge-soft.php) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from ebuild RDEPEND/DEPEND)

- Gentoo packages: `dev-lang/nasm`

Backlinks: [[cracker]] · [[md5bf]] · [[Pentoo Gap Analysis]]
