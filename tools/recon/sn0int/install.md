---
tool: "sn0int"
build-system: "rust"
upstream: "https://github.com/kpcyrd/sn0int"
recipe: "upstream"
license: "GPLv3+"
---

# Installing sn0int

> Semi-automatic OSINT framework and package manager for recon and attack-surface enumeration.

Source: upstream [`kpcyrd/sn0int`](https://github.com/kpcyrd/sn0int). Also packaged by Alpine, OpenBSD, Gentoo/Pentoo, Homebrew and Fedora — see the Repology badge in the README.

## Arch Linux

```bash
sudo pacman -S sn0int
```

## Nix (works on any distro)

```bash
nix-env -i sn0int    # or: nix run nixpkgs#sn0int
```

## Debian / Ubuntu / Kali (upstream apt repo)

```bash
sudo apt install curl sq
curl -sSf https://apt.vulns.sexy/kpcyrd.pgp | sq dearmor | sudo tee /etc/apt/trusted.gpg.d/apt-vulns-sexy.gpg > /dev/null
echo deb http://apt.vulns.sexy stable main | sudo tee /etc/apt/sources.list.d/apt-vulns-sexy.list
sudo apt update
```

## Docker

```bash
docker run --rm --init -it -v "$PWD/.cache:/cache" -v "$PWD/.data:/data" ghcr.io/kpcyrd/sn0int
```

## Build from source

Rust project — see the upstream [build docs](https://sn0int.readthedocs.io/en/latest/build.html) for per-distro dependencies.

Backlinks: [[recon]] · [[sn0int]]
