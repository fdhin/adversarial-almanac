---
name: "triton"
categories:
  - blackarch-binary
version: "4382.bc84cf74"
homepage: "https://github.com/JonathanSalwan/Triton"
packaged-by: [blackarch, arch, aur, nixpkgs]
blackarch-builddate: "2026-11-01"
arch-builddate: "2026-03-30"
build-system: cmake
license: BSD
meta-checked: "2026-11-09"
github-repo: "JonathanSalwan/Triton"
github-stars: 4361
github-pushed: "2026-11-07"
github-archived: false
github-issues: 48
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 111
gh-activity-checked: "2026-11-09"
---

# triton

> Dynamic binary analysis library for concolic execution, taint tracking and SMT solving over instruction semantics.

## What it's for

Triton is a C++ library with Python bindings for building program-analysis tools: dynamic symbolic execution, taint analysis, instruction semantics for x86, x86-64, ARM32, AArch64 and RISC-V (32/64), SMT simplification passes, expression synthesis, and lifting to LLVM-IR or Z3 with solver interfaces to Z3 and Bitwuzla. Typical uses are deobfuscation and devirtualization, ROP-chain generation and automated vulnerability research. It is a library, not a tracer or CLI front-end: you feed it instructions plus concrete register/memory state from your own driver (a DBI tool, an emulator, or a framework like TritonDSE) and then query the symbolic state it maintains.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Execute instructions and inspect their symbolic state

```python
from triton import ARCH, Instruction, TritonContext

ctx = TritonContext(ARCH.X86_64)
ctx.setConcreteRegisterValue(ctx.registers.rip, 0x40000)
ctx.symbolizeRegister(ctx.registers.rax, "my_rax")
ctx.processing(Instruction(b"\x48\x35\x34\x12\x00\x00"))  # xor rax, 0x1234
ctx.processing(Instruction(b"\x48\x89\xc1"))              # mov rcx, rax
print(ctx.getSymbolicRegister(ctx.registers.rcx))
```

Outcome: prints the SMT expression tied to rcx, e.g. `(define-fun ref!8 () (_ BitVec 64) ref!1) ; MOV operation ...` — the symbolic formula built by the two processed instructions, with `my_rax` as the symbolic root.

### Solve for an input that reaches a target value

```python
model = ctx.getModel(ctx.getSymbolicRegister(ctx.registers.rcx).getAst() == 0xdead)
print(model)
```

Outcome: queries the SMT solver and prints a model such as `{0: my_rax:64 = 0xcc99}` — the concrete input value that makes the expression equal 0xdead, the primitive behind concolic branch inversion.
