---
tool: "sslcat"
build-system: "make"
upstream: "http://www.bindshell.net/tools/sslcat"
recipe: "pentoo-ebuild"
version: "1.0"
license: "BSD"
---

# Installing sslcat

> netcat like application with ssl support

Recipe source of truth: Pentoo [`sslcat.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/net-analyzer/sslcat) (version `1.0`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask sslcat
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss sslcat`).

## Any other Linux distro

**From source (needs gcc + make):**

```bash
curl -fLO http://www.bindshell.net/tools/sslcat/sslcat.tgz
tar xf sslcat.tgz
cd sslcat
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from ebuild RDEPEND/DEPEND)

- Gentoo packages: `dev-libs/openssl`

Backlinks: [[scanner]] · [[sslcat]] · [[Pentoo Gap Analysis]]
