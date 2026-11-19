---
tool: "ipatool"
build-system: "go"
upstream: "https://github.com/majd/ipatool"
recipe: "upstream"
license: "MIT"
---

# Installing ipatool

> Search, license and download App Store packages from the terminal.

Source: pentest-book feed; upstream [`majd/ipatool`](https://github.com/majd/ipatool).

## Arch Linux (AUR)

```bash
paru -S ipatool
```

## NixOS / nixpkgs

Also packaged in nixpkgs — resolve the attribute with:

```bash
nix search nixpkgs ipatool
```

## macOS

```bash
brew install ipatool
```

## Other Linux distro (release binary)

Grab the latest binary for your platform from
[upstream releases](https://github.com/majd/ipatool/releases) and put it
on your `PATH`.

## Build from source

Requires the Go toolchain.

```bash
git clone https://github.com/majd/ipatool.git
cd ipatool
go build -o ipatool
```

Backlinks: [[mobile]] · [[ipatool]]
