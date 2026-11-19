---
tool: "rustscan"
build-system: "rust"
upstream: "https://github.com/RustScan/RustScan"
recipe: "upstream"
license: "GPL-3.0"
nix-package: "rustscan"
---

# Installing rustscan

> The Modern Port Scanner: fast, smart, effective — full-port scans piped into nmap.

Source: pentest-book feed; upstream [`RustScan/RustScan`](https://github.com/RustScan/RustScan). Usage docs live in the [project wiki](https://github.com/RustScan/RustScan/wiki/Usage).

## Arch Linux

```bash
sudo pacman -S rustscan    # official [extra] repo
```

## Nix / NixOS

```bash
nix-shell -p rustscan          # ad-hoc shell
nix-env -iA nixpkgs.rustscan   # profile install
```

## Package managers / source

```bash
brew install rustscan            # macOS
cargo install rustscan           # from crates.io (upstream's officially supported route)
```

## Prebuilt binaries

Download from [upstream releases](https://github.com/RustScan/RustScan/releases).

Backlinks: [[scanner]] · [[rustscan]]
