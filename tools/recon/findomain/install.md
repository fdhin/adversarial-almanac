---
tool: "findomain"
build-system: "rust"
upstream: "https://github.com/Findomain/Findomain"
recipe: "upstream"
license: "GPL-3.0"
nix-package: "findomain"
---

# Installing findomain

> The complete solution for domain recognition: CT-log subdomain enumeration, monitoring, and more.

Source: pentest-book feed; upstream [`Findomain/Findomain`](https://github.com/Findomain/Findomain). Detailed instructions (access tokens, monitoring setup, platforms): [`docs/INSTALLATION.md`](https://github.com/Findomain/Findomain/blob/master/docs/INSTALLATION.md) in the repo.

## Arch Linux

```bash
sudo pacman -S findomain    # official [extra] repo
```

## Nix / NixOS

```bash
nix-shell -p findomain          # ad-hoc shell
nix-env -iA nixpkgs.findomain   # profile install
```

## Prebuilt binaries

Download from [upstream releases](https://github.com/Findomain/Findomain/releases) — 64-bit Linux, Windows, macOS, and Aarch64 builds are published.

## API tokens

Many sources require access tokens; see [Access Tokens Configuration](https://github.com/Findomain/Findomain/blob/master/docs/INSTALLATION.md#access-tokens-configuration) before counting on them.

Backlinks: [[recon]] · [[findomain]]
