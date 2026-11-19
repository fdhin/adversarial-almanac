---
tool: "peda"
build-system: "python"
upstream: "https://github.com/longld/peda"
recipe: "upstream"
license: "custom:unknown"
---

# Installing peda

> PEDA — classic GDB exploit-development assistance plugin (checksec, cyclic patterns, ROP search).

Source: HackTricks feed admission; upstream [`longld/peda`](https://github.com/longld/peda).

## Standard install

```bash
git clone https://github.com/longld/peda.git ~/peda
echo "source ~/peda/peda.py" >> ~/.gdbinit
```

No build step — PEDA is a single Python script sourced by GDB at startup.
Verify inside GDB with `peda help`.

## Compatibility note

Upstream is dormant (last push 2024) and predates Python-3-only GDB builds.
On modern GDB (Python 3 bindings) use the dual-compatible community fork:

```bash
git clone https://github.com/zachriggle/peda.git ~/peda
echo "source ~/peda/peda.py" >> ~/.gdbinit
```

Alternatives in the same niche (actively maintained, Python 3): GEF, pwndbg.

## Not packaged

No mainstream distro packages peda — the `.gdbinit` source line is the only
install mechanism. Checked 2026-11-13.

Backlinks: [[debugger]] · [[peda]]
