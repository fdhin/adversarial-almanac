---
name: "ssh-audit-install"
categories:
  - scanner
---

# Installing ssh-audit

Python package (no dependencies, Python 3.10+); PyPI is the canonical channel:

```bash
pip3 install ssh-audit
```

## Arch / Nix

```bash
sudo pacman -S ssh-audit        # official Arch repo
nix-shell -p ssh-audit          # nixpkgs attribute: ssh-audit
```

## Docker / Snap

```bash
docker pull positronsecurity/ssh-audit
docker run -it --rm -p 2222:2222 positronsecurity/ssh-audit target.example.com
snap install ssh-audit
```

## From source

```bash
git clone https://github.com/jtesta/ssh-audit
cd ssh-audit
pip3 install .
```

Verification: `ssh-audit -V` (version) or `ssh-audit -m` for the built-in man page.
