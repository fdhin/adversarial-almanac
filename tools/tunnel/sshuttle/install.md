---
tool: "sshuttle"
build-system: "python"
upstream: "https://github.com/sshuttle/sshuttle"
recipe: "upstream"
license: "LGPL-2.1"
---

# Installing sshuttle

> Transparent proxy + VPN over SSH: route subnets through an SSH server with nothing installed on the remote side.

Source: pentest-book feed; upstream [`sshuttle/sshuttle`](https://github.com/sshuttle/sshuttle). Docs: <https://sshuttle.readthedocs.io>.

## Arch Linux

```bash
sudo pacman -S sshuttle
```

## Nix (works on any distro)

```bash
nix-shell -p sshuttle          # ad-hoc environment
nix-env -iA nixos.sshuttle     # or persist it
```

## From PyPI

```bash
sudo pip install sshuttle
```

(user-level alternative: `pip install sshuttle` — needs sudo only at run time, since sshuttle must manage firewall rules locally).

## From source

```bash
git clone https://github.com/sshuttle/sshuttle.git
cd sshuttle
sudo pip install .
```

## Notes

- Nothing to install on the remote SSH server — it only needs Python; sshuttle uploads its source at connect time.
- Root/sudo is required locally to set up the transparent proxy (firewall rules).
- Runs on Linux, FreeBSD, macOS; Windows is experimental (see the docs' Windows section).

Backlinks: [[tunnel]] · [[sshuttle]]
