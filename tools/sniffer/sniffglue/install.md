---
tool: "sniffglue"
build-system: "rust"
upstream: "https://github.com/kpcyrd/sniffglue"
recipe: "upstream"
license: "GPLv3"
---

# Installing sniffglue

> Secure multithreaded Rust sniffer printing useful traffic summaries by default.

Source: upstream [`kpcyrd/sniffglue`](https://github.com/kpcyrd/sniffglue). Also packaged by Debian (bullseye+), Ubuntu (21.04+), Kali, Alpine, Gentoo/Pentoo, Guix, Homebrew and a Fedora COPR — see the Repology badge in the README.

## Arch Linux

```bash
sudo pacman -S sniffglue
```

## Nix (works on any distro)

```bash
nix-env -i sniffglue    # or: nix run nixpkgs#sniffglue
```

## Build from source (any distro)

Needs libpcap and libseccomp headers plus a Rust toolchain:

```bash
sudo apt install libpcap-dev libseccomp-dev   # debian-based; equivalents elsewhere
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source $HOME/.cargo/env
cargo install sniffglue
sniffglue --help
```

A Debian package can be built from a clone with `cargo deb`.

Backlinks: [[sniffer]] · [[sniffglue]]
