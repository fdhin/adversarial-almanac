---
tool: "extundelete"
build-system: "autotools"
upstream: "http://extundelete.sourceforge.net/"
recipe: "kali-debian"
nix-package: "extundelete"
aur-package: "extundelete"
version: "0.2.4-3+b2"
---

# Installing extundelete

> utility to recover deleted files from ext3/ext4 partition extundelete uses the information stored in the partition's journal to attempt to recover a file that has been deleted. There is no guarantee that any particular file will be able to be undeleted.

Recipe source of truth: Kali's [`debian/` packaging](https://gitlab.com/kalilinux/packages/extundelete) (version `0.2.4-3+b2`).

## Kali / Debian-based

```bash
sudo apt install extundelete
```

## Arch / BlackArch

Not packaged. Check the AUR (`paru -Ss extundelete`); otherwise build from source below.

## Nix (works on any distro)

```bash
nix run nixpkgs#extundelete    # ad-hoc run, no install
nix profile install nixpkgs#extundelete   # or persist it
```

## Any other Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://extundelete.sourceforge.net/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from debian/control)

- Build (Debian names): `autotools-dev`, `e2fslibs-dev`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@ 
```

Backlinks: [[forensic]] · [[extundelete]] · [[Kali Gap Analysis]]
