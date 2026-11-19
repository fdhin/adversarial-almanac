---
tool: "gron"
build-system: "go"
upstream: "https://github.com/tomnomnom/gron"
recipe: "upstream"
version: "git (2025-05-31)"
license: "see repository"
---

# Installing gron

> Make JSON greppable!

Source: upstream [`tomnomnom/gron`](https://github.com/tomnomnom/gron); discovered via the awesome-oneliner-bugbounty review.

## Arch / BlackArch

```bash
sudo pacman -S gron
```

## Nix (any distro)

```bash
nix run nixpkgs#gron
```

## From source

```bash
git clone https://github.com/tomnomnom/gron.git
cd gron
go build ./...
```

Backlinks: [[misc]] · [[gron]]
