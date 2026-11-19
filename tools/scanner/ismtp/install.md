---
tool: "ismtp"
build-system: "python"
upstream: "https://github.com/altjx/ipwn/"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/i/ismtp"
version: "1.6+git20190922-0parrot1"
---

# Installing ismtp

> SMTP user enumeration and testing tool Test for SMTP user enumeration (RCPT TO and VRFY), internal spoofing, and relay.

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install ismtp
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss ismtp`); otherwise build from source.

## Any other Linux distro

**From source:**

```bash
git clone https://github.com/altjx/ipwn/
cd ipwn
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from debian/control)

- Build (Debian names): `python3-all`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@ --with python3
override_dh_install:
```

Backlinks: [[scanner]] · [[ismtp]] · [[Parrot Gap Analysis]]
