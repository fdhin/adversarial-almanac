---
tool: "whatmask"
build-system: "scripts"
upstream: "http://www.laffeycomputer.com/whatmask.html"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/w/whatmask"
version: "1.2-0parrot1"
---

# Installing whatmask

> helper for network settings This package contains a small C program that will help you with network settings. Whatmask can work in two modes. The first mode is to invoke Whatmask with only a subnet ma

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install whatmask
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss whatmask`); otherwise build from source.

## Any other Linux distro

**From source:**

**Source:** download the release from [upstream site](http://www.laffeycomputer.com/whatmask.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@
```

Backlinks: [[networking]] · [[whatmask]] · [[Parrot Gap Analysis]]
