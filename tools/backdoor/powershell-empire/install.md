---
tool: "powershell-empire"
build-system: "python"
upstream: "https://github.com/BC-SECURITY/Empire"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/p/powershell-empire"
version: "6.6.0-0parrot7"
---

# Installing powershell-empire

> PowerShell and Python post-exploitation agent This package contains a post-exploitation framework that includes a pure-PowerShell2.0 Windows agent, and a pure Python Linux/OS X agent. It is the merge 

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install powershell-empire
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss powershell-empire`); otherwise build from source.

## Any other Linux distro

**From source:**

```bash
git clone https://github.com/BC-SECURITY/Empire
cd Empire
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from debian/control)

- Python (pip): `venv`, `pip`, `dev`
- Runtime (Debian names): `git`, `python3:any`, `mariadb-server`
- Build (Debian names): `git`, `python3-dev`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@ --with python-virtualenv
	cp -r $(CURDIR)/setup $(CURDIR)/debian/powershell-empire/usr/share/powershell-empire/lib/python3*/site-packages/
	rm -rf $(CURDIR)/debian/powershell-empire/usr/share/powershell-empire/lib/python3*/site-packages/empire/test
override_dh_strip:
override_dh_dwz:
```

Backlinks: [[backdoor]] · [[powershell-empire]] · [[Parrot Gap Analysis]]
