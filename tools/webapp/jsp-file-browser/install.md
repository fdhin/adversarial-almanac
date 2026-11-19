---
tool: "jsp-file-browser"
build-system: "scripts"
upstream: "https://www.vonloesch.de/filebrowser.html"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/j/jsp-file-browser"
version: "1.2-0parrot1"
---

# Installing jsp-file-browser

> File browser java server page This package contains an easy to use and easy to install file browser java server page. This JSP program allows remote web-based file access and manipulation. Features:  

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install jsp-file-browser
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss jsp-file-browser`); otherwise build from source.

## Any other Linux distro

**From source:**

**Source:** download the release from [upstream site](https://www.vonloesch.de/filebrowser.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@
```

Backlinks: [[webapp]] · [[jsp-file-browser]] · [[Parrot Gap Analysis]]
