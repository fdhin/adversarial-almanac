---
tool: "whois"
build-system: "make"
upstream: "https://github.com/rfc1036/whois"
recipe: "upstream"
version: "git (2026-07-25)"
license: "see repository"
---

# Installing whois

> Intelligent WHOIS client

Source: upstream [`rfc1036/whois`](https://github.com/rfc1036/whois); discovered via the awesome-oneliner-bugbounty review.

## Arch / BlackArch

```bash
sudo pacman -S whois
```

## Nix (any distro)

```bash
nix run nixpkgs#whois
```

## From source

```bash
git clone https://github.com/rfc1036/whois.git
cd whois
make
sudo make install
```

Backlinks: [[recon]] · [[whois]]
