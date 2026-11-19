---
tool: "nikto"
build-system: "perl"
upstream: "https://github.com/sullo/nikto"
recipe: "upstream"
license: "GPL-3.0"
nix-package: "nikto"
---

# Installing nikto

> Nikto web server scanner — dangerous files, misconfigurations, and outdated components.

Source: corpus + pentest-book feed; upstream [`sullo/nikto`](https://github.com/sullo/nikto). Full documentation: the [project wiki](https://github.com/sullo/nikto/wiki) and [cirt.net/Nikto2](https://cirt.net/Nikto2).

## Arch Linux

```bash
sudo pacman -S nikto    # official [extra] repo
```

## Nix / NixOS

```bash
nix-shell -p nikto          # ad-hoc shell
nix-env -iA nixpkgs.nikto   # profile install
```

## Run straight from the repo (no install)

```bash
git clone https://github.com/sullo/nikto
cd nikto/program
./nikto.pl -h http://www.example.com    # or: perl nikto.pl -h ...
```

Needs only Perl core modules.

## Docker

```bash
docker pull ghcr.io/sullo/nikto:latest
docker run --rm ghcr.io/sullo/nikto:latest -h http://www.example.com
```

## Licensing note

The Perl code is GPL version 3, but the database files are NOT GPL-licensed and may only be distributed as part of the official Nikto package — see [cirt.net/Nikto-Licensing](https://cirt.net/Nikto-Licensing) before redistributing.

Backlinks: [[scanner]] · [[nikto]]
