---
name: "qbdi"
categories:
  - blackarch-binary
version: "v0.12.1.r12.g3318580"
homepage: "https://github.com/QBDI/QBDI"
packaged-by: [blackarch]
blackarch-builddate: "2026-08-24"
build-system: cmake
license: Apache-2.0
meta-checked: "2026-11-09"
github-repo: "QBDI/QBDI"
github-stars: 1835
github-pushed: "2026-09-02"
github-archived: false
github-issues: 20
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 42
gh-activity-checked: "2026-11-09"
---

# qbdi

> A modular, cross-platform dynamic binary instrumentation framework built on LLVM.

## What it's for

QBDI (QuarkslaB Dynamic binary Instrumentation) is a library framework for
instrumenting native code at runtime on Linux, macOS, Android, iOS and Windows
across x86, x86-64, ARM and AArch64. It exposes a C/C++ API plus Python
bindings (PyQBDI) and JavaScript bindings that integrate with Frida, so a
script can trace executed instructions, record memory accesses, or collect
code coverage without writing a DBI engine from scratch. By design it ships no
preferred injection mechanism and is meant to be paired with an external
injector; it bundles QBDIPreload, a tiny `LD_PRELOAD`-based injector for
dynamically linked executables, and PyQBDIPreload wraps that flow for Python
scripts. Known core limitations: it does not handle signals, newly created
threads, or C++ exception unwinding.

## Typical use cases

### Trace every instruction an executable runs

Write a preload script that registers a pre-instruction callback, then launch
the target through PyQBDIPreload.

```
cat > trace_preload.py <<'EOF'
import pyqbdi

def showInstruction(vm, gpr, fpr, data):
    inst = vm.getInstAnalysis()
    print("0x{:x}: {}".format(inst.address, inst.disassembly))
    return pyqbdi.CONTINUE

def pyqbdipreload_on_run(vm, start, stop):
    vm.addCodeCB(pyqbdi.PREINST, showInstruction, None)
    vm.run(start, stop)
EOF
python3 -m pyqbdipreload trace_preload.py ./target arg1
```

Outcome: the target is started with the Python runtime injected via
`LD_PRELOAD`; every executed instruction of the main program is reported as an
`address: disassembly` line on stdout, giving a complete instruction trace
without modifying the target binary.

### Instrument a single native function inside a Python process

Load a shared library with ctypes and run one of its functions on a QBDI
virtual machine with memory-access recording enabled.

```
python3 - <<'EOF'
import ctypes, struct, pyqbdi

libm = ctypes.cdll.LoadLibrary("libm.so.6")
func = ctypes.cast(libm.sin, ctypes.c_void_p).value

vm = pyqbdi.VM()
state = vm.getGPRState()
stack = pyqbdi.allocateVirtualStack(state, 0x100000)
vm.addInstrumentedModuleFromAddr(func)
vm.recordMemoryAccess(pyqbdi.MEMORY_READ_WRITE)

def insnCB(vm, gpr, fpr, data):
    inst = vm.getInstAnalysis()
    print("0x{:x}: {}".format(inst.address, inst.disassembly))
    return pyqbdi.CONTINUE

vm.addCodeCB(pyqbdi.PREINST, insnCB, None)
fpr = vm.getFPRState()
fpr.xmm0 = struct.pack('<d', 1.0)
pyqbdi.simulateCall(state, 0x42424242)
ok = vm.run(func, 0x42424242)
print("run ok:", ok, "sin(1.0) =", struct.unpack('<d', fpr.xmm0[:8])[0])
pyqbdi.alignedFree(stack)
EOF
```

Outcome: only code from the instrumented module is executed under QBDI; the
callback prints each instruction of `sin()` and its memory accesses are
recorded, while the final register state shows the function's return value —
a self-contained harness for tracing library code.

### Collect basic-block coverage in drcov format

Run the upstream `coverage.py` example (a PyQBDIPreload script) against a
target to log every basic block entered.

```
python3 -m pyqbdipreload coverage.py ./target
```

Outcome: basic-block-entry events are matched to their containing module and,
on process exit, written to `a.cov` in DynamoRIO's drcov format — directly
loadable by coverage visualization and differential-testing tooling.

*No corpus evidence — derived from upstream documentation.*
