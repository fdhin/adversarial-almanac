---
tool: "pwndbg"
build-system: "python"
upstream: "https://github.com/pwndbg/pwndbg"
recipe: "upstream"
license: "MIT"
---

# Installing pwndbg

> A GDB and LLDB plug-in that makes debugging suck less, focused on exploit development and reverse engineering.

Source: pentest-book feed; upstream [`pwndbg/pwndbg`](https://github.com/pwndbg/pwndbg). Docs: <https://pwndbg.re/stable/setup>.

## Arch Linux

```bash
sudo pacman -S pwndbg
```

## Any other Linux distro (upstream installer)

```bash
git clone https://github.com/pwndbg/pwndbg
cd pwndbg
./setup.sh
```

The setup script installs the Python dependencies and hooks pwndbg into your `~/.gdbinit` so every future `gdb` session loads it.

## Notes

- Requirements (GDB side): Python 3.10+ and GDB 12.1+; the LLDB side needs Python 3.12+ and LLDB 19+ and is still early-stage.
- qemu-user debugging needs QEMU 8.1+ (vFile API), qemu-system 6.2+.
- The repository ships a `flake.nix` for Nix users, but pwndbg is not in nixpkgs — use the installer above.
- After installing, launch `gdb` as usual; pwndbg's banner and commands (`vmmap`, `heap`, `checksec`, ...) confirm it loaded.

Backlinks: [[debugger]] · [[pwndbg]]
