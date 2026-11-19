---
name: "manticore"
categories:
  - blackarch-binary
version: "0.3.7.r88.gcdf6c4b"
homepage: "https://github.com/trailofbits/manticore"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-06-29"
build-system: python
license: AGPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "trailofbits/manticore"
github-stars: 3856
github-pushed: "2026-06-24"
github-archived: true
github-issues: 261
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.8
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 6
gh-activity-checked: "2026-11-09"
---

# manticore

> Symbolic execution for Linux ELF binaries, Ethereum EVM bytecode, and WASM modules — CLI plus Python API (project archived).

## What it's for

Manticore (Trail of Bits) executes programs with symbolic inputs: it
explores all reachable states, generates concrete inputs that reach each
state, and detects crashes and assertion failures — for smart contracts it
also runs detector plugins (uninitialized memory, reentrancy,
external-call leaks). The CLI (`manticore`) performs quick analyses of a
single ELF binary or Solidity/Vyper contract, while the Python API
(`Manticore.linux`, `ManticoreEVM`, `ManticoreWASM`) supports custom
analyses with instruction hooks and event callbacks. Supported targets:
Linux x86/x86_64/ARMv7/aarch64 ELF binaries, EVM bytecode (requires `solc`
in PATH; contracts are built via crytic-compile), and WASM modules. The
upstream repository is archived — no longer developed internally — but the
tool remains packaged and usable.

## Typical use cases

### Symbolically execute a Linux binary

```
manticore ./target
```

Outcome: Manticore runs the ELF with symbolic input, logs
`Generated testcase No. N` for each terminated state along with its exit
status, and writes all per-state inputs and logs into a `mcore_...`
workspace directory.

### Analyze a smart contract for reachable failures

```
manticore contract.sol
```

Outcome: the `.sol`/`.vy` extension is auto-detected; Manticore compiles
the contract, drives symbolic transactions through it, and generates one
testcase per distinct outcome (STOP, THROW, or a detector finding such as
reentrancy), again under `mcore_...`.

### Choose the constraint solver

```
manticore --smt.solver Z3 ./target
```

Outcome: the SMT solver is pinned to Z3 instead of the auto-selection
(Yices when installed, otherwise Z3 or CVC4).

### Hook an address with a custom analysis

```python
from manticore.native import Manticore

m = Manticore.linux('./target')

@m.hook(0x400ca0)
def hook(state):
    print('eax', state.cpu.EAX)
    m.kill()

m.run()
```

Outcome: exploration runs until a state reaches 0x400ca0, where the hook
reads registers or memory and stops the run — the recipe for targeted
analyses that avoid whole-program state explosion.

### Verify a contract property with the EVM API

```python
from manticore.ethereum import ManticoreEVM

contract_src = """
contract Adder {
    function incremented(uint value) public returns (uint){
        if (value == 1)
            revert();
        return value + 1;
    }
}
"""
m = ManticoreEVM()
user_account = m.create_account(balance=10000000)
contract_account = m.solidity_create_contract(contract_src,
                                              owner=user_account,
                                              balance=0)
value = m.make_symbolic_value()
contract_account.incremented(value)

for state in m.ready_states:
    print("can value be 1? {}".format(state.can_be_true(value == 1)))
```

Outcome: after the symbolic call, each ready state answers `can_be_true` —
whether some concrete input makes `value` equal 1 (the reverting branch) —
the property-checking pattern that the `manticore-verifier` CLI
formalizes.

*No corpus evidence — derived from upstream documentation.*
