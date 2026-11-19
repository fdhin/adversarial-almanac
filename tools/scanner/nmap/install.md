---
tool: "nmap"
build-system: "c"
upstream: "https://github.com/nmap/nmap"
recipe: "upstream"
version: "git (2026-11-05)"
license: "see repository"
---

# Installing nmap

> Nmap - the Network Mapper. Github mirror of official SVN repository.

Source: upstream [`nmap/nmap`](https://github.com/nmap/nmap); discovered via the awesome-oneliner-bugbounty review.

## Arch / BlackArch

```bash
sudo pacman -S nmap
```

## Kali / Debian-based

```bash
sudo apt install nmap
```

## Nix (any distro)

```bash
nix run nixpkgs#nmap
```

## From source

```bash
git clone https://github.com/nmap/nmap.git
cd nmap
make
sudo make install
```

Backlinks: [[scanner]] · [[nmap]]
