---
tool: "fritap"
build-system: "python"
upstream: "https://github.com/fkie-cad/friTap"
recipe: "upstream"
license: "see repository"
---

# Installing fritap

> Frida-based real-time SSL/TLS key extraction and traffic decryption for security research.

Source: upstream [`fkie-cad/friTap`](https://github.com/fkie-cad/friTap).

## pip (the supported path)

```bash
pip3 install fritap    # gives you the `fritap` command
pip3 install --upgrade fritap
```

Requires Python >= 3.10; each friTap major tracks one Frida major (2.x needs frida 17.x + frida-tools 14.x, pulled in automatically).

## Older frida-server on the device

Pin a matching legacy friTap with the upstream constraints file:

```bash
pip install fritap==1.4.3.0 -c https://raw.githubusercontent.com/fkie-cad/friTap/main/constraints/frida16.txt
```

(or `python dev/install_legacy.py --frida-major 16` from a clone; recipes for frida 15/16/17-legacy live in the repo's `constraints/` directory).

## Mobile targets

- Android/iOS analysis needs `frida-server` running on the device and `adb` in PATH.
- Optional for full packet capture (`-f/--full_capture`): libpcap (Linux/macOS) or Npcap (Windows).

## Not packaged by distros

No Arch/AUR/nixpkgs package yet (checked 2026-11-14) — use pip.

Backlinks: [[sniffer]] · [[fritap]]
