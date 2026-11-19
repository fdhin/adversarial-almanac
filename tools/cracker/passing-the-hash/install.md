---
tool: "passing-the-hash"
build-system: "autotools"
upstream: "http://passing-the-hash.blogspot.fr"
recipe: "kali-debian"
version: "0~2015.12.37+b1"
---

# Installing passing-the-hash

> Patched tools to use password hashes as authentication input This package contains modified versions of Curl, Iceweasel, FreeTDS, Samba 4, WinEXE and WMI. They are installed as executables starting with the "pth-" string.

Recipe source of truth: Kali's [`debian/` packaging](https://gitlab.com/kalilinux/packages/passing-the-hash) (version `0~2015.12.37+b1`).

## Kali / Debian-based

```bash
sudo apt install passing-the-hash
```

## Arch / BlackArch

Not packaged. Check the AUR (`paru -Ss passing-the-hash`); otherwise build from source below.

## Any other Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://passing-the-hash.blogspot.fr) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from debian/control)

- Runtime (Debian names): `samba-common-bin`, `smbclient`, `sqsh`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@
override_dh_makeshlibs:
override_dh_auto_clean:
override_dh_auto_configure::
	dh_auto_configure
	[ -d build-tree ] || ./prepare-build-tree.sh
override_dh_auto_build::
override_dh_auto_install::
override_dh_auto_configure::
	cd build-tree/curl && ./configure --with-gssapi --enable-static --disable-shared --with-openssl --without-libpsl
override_dh_auto_build::
override_dh_auto_install::
override_dh_auto_build::
```

Backlinks: [[cracker]] · [[passing-the-hash]] · [[Kali Gap Analysis]]
