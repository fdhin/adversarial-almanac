---
tool: "gef"
build-system: "python"
upstream: "https://github.com/bata24/gef"
recipe: "upstream"
license: "MIT"
---

# Installing gef (bata24 fork)

> bata24's GEF fork: kernel-debugging heuristics, multi-allocator heap tooling, wide qemu-user arch support.

Source: HackTricks feed admission; upstream [`bata24/gef`](https://github.com/bata24/gef).
For the original upstream GEF (hugsy) — the one distros package — see [[gdb-gef]].

## Install (installer refactored 2026-10; re-install if older)

```bash
# as root (recommended upstream)
wget -q https://raw.githubusercontent.com/bata24/gef/dev/install-uv.sh -O- | sudo sh

# non-root
wget -q https://raw.githubusercontent.com/bata24/gef/dev/install-uv.sh -O- | sh

# custom directory
wget -q https://raw.githubusercontent.com/bata24/gef/dev/install-uv.sh -O- | env GEF_INSTALL_DIR="/path/to/gef" sh
```

By default `gef.py` lands in `$HOME/.gef/gef.py`, required Python packages go
into `$HOME/.gef/venv-gef`, and the installer picks a CPython matching GDB's
embedded Python major/minor — no manual venv activation is needed at startup.

Supported environments: Ubuntu 24.04–26.04, Debian stable, Arch Linux
recommended; Ubuntu 20.04–23.10 may work.

## Upgrade

```bash
python3 "$HOME/.gef/gef.py" --upgrade
```

Restart GDB afterwards; if errors appear after upgrading, rename the old
`$HOME/.gef.rc`. If GDB's Python version changes, recreate `venv-gef` to
match and reinstall its packages.

## Not packaged under this identity

Kali's and nixpkgs's `gef` packages build the original hugsy/gef, and
Parrot's `gdb-gef` likewise — this fork is install-via-script only.
Checked 2026-11-13.

Backlinks: [[debugger]] · [[gef]]
