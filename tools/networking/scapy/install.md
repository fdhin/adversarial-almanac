---
tool: "scapy"
build-system: "python"
upstream: "https://github.com/secdev/scapy"
recipe: "upstream"
license: "GPL-2.0"
---

# Installing scapy

> Python packet-crafting toolkit: forge, send, sniff, match and replay any protocol stack from an interactive shell or a library.

Source: hacktricks feed; upstream [`secdev/scapy`](https://github.com/secdev/scapy).

## Distro packages

```bash
pacman -S scapy        # Arch (also in BlackArch)
apt install scapy      # Debian/Kali
```

## From source / standalone

```bash
git clone https://github.com/secdev/scapy
cd scapy
sudo ./run_scapy
```

`./run_scapy` runs straight from the checkout (root needed for raw sockets).
Optional extras for plotting/crypto features: `pip install matplotlib cryptography`.

## Notes

Runs on Python 3.9+; on Windows additional dependencies are required — see
the [installation docs](https://scapy.readthedocs.io/en/latest/installation.html).

Backlinks: [[networking]] · [[scapy]]
