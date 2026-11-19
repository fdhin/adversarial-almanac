---
name: "barf"
categories:
  - blackarch-binary
version: "923.9547ef8"
homepage: "https://github.com/programa-stic/barf-project"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: python
license: BSD
meta-checked: "2026-11-09"
github-repo: "programa-stic/barf-project"
github-stars: 1456
github-pushed: "2019-11-24"
github-archived: false
github-issues: 17
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# barf

> Python binary-analysis framework built on the REIL intermediate language, with ROP-gadget, CFG and call-graph tools.

## What it's for

BARF loads ELF and PE binaries for x86/x64 and ARM32, lifts instructions to the REIL intermediate language so that every analysis is architecture-agnostic, and integrates the Z3 and CVC4 SMT solvers so code fragments can be expressed as formulae with constraints checked on them (for example, solving which input values produce a given register result). The package also installs command-line tools built on the framework: BARFgadgets (search, classify and verify ROP gadgets), BARFcfg (recover control-flow graphs) and BARFcg (recover call graphs). Development stopped around 2019 at v0.6.0 — expect a Python-2-era codebase and plan accordingly.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Disassemble a binary with REIL translation

See each native instruction next to its intermediate-language form.

```
python3 - <<'PY'
from barf import BARF
barf = BARF("./target")
for addr, asm_instr, reil_instrs in barf.translate():
    print("{:#x} {}".format(addr, asm_instr))
PY
```

Outcome: prints the disassembly of the loaded binary address by address; extend the loop to also print each instruction's `reil_instrs` entries.

### Recover a control-flow graph to a dot file

Produce a graphable CFG without leaving Python.

```
python3 - <<'PY'
from barf import BARF
barf = BARF("./target")
cfg = barf.recover_cfg()
cfg.save("target_cfg")
PY
```

Outcome: recovers the control flow of the program's functions and writes a Graphviz `.dot` file that `dot -Tpdf target_cfg.dot` renders to a picture.

### Find and verify ROP gadgets

Collect exploit building blocks with verified semantics.

```
BARFgadgets -c -v ./target
```

Outcome: searches for ret/jmp/call-ended gadgets, classifies them by emulated semantics (move register, load constant, arithmetic, memory access, ...) and SMT-verifies each classification; `-u` removes duplicates and `-r 64` filters verified gadgets by operand register size.

### Export CFGs or call graphs for all functions

Whole-program graphs from the command line.

```
BARFcfg -a -f dot -d cfg_out ./target
BARFcg -a -f dot ./target
```

Outcome: the first command writes per-function CFG `.dot` files into `cfg_out/`; the second recovers the program's call graph — both also support pdf and png output formats via `-f`.
