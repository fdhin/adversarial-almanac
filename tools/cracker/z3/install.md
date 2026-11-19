---
tool: "z3"
build-system: "cmake"
upstream: "https://github.com/Z3Prover/z3"
recipe: "upstream"
license: "MIT"
---

# Installing z3

> Microsoft Research's SMT theorem prover: state constraints over integers, bitvectors and strings, and let it find the satisfying input.

Source: hacktricks feed; upstream [`Z3Prover/z3`](https://github.com/Z3Prover/z3).

## Distro packages

```bash
pacman -S z3            # Arch (also in BlackArch)
apt install z3          # Debian/Kali (z3 + libz3 + python3-z3)
```

## Python bindings (PyPI)

```bash
pip install z3-solver
```

Ships `libz3` plus the `z3` module; no separate native install needed.

## Prebuilt binaries

Stable and nightly builds for Linux/macOS/Windows:
[github.com/Z3Prover/z3/releases](https://github.com/Z3Prover/z3/releases).

## Build from source (CMake)

```bash
git clone https://github.com/Z3Prover/z3.git
cd z3
cmake -S . -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build --parallel $(nproc)
sudo cmake --install build
```

Add `-DZ3_BUILD_PYTHON_BINDINGS=ON` (and a matching
`-DCMAKE_INSTALL_PYTHON_PKG_DIR=`) to also install the Python API.

Backlinks: [[cracker]] · [[z3]]
