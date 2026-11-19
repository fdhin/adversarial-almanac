---
name: "crypto-attacks"
categories:
  - crypto
version: "git (2026-01-08)"
homepage: "https://github.com/jvdsn/crypto-attacks"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: python
meta-checked: "2026-11-09"
github-repo: "jvdsn/crypto-attacks"
github-stars: 1294
github-pushed: "2026-01-08"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
worth: 5.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 8
---

# crypto-attacks

> SageMath library of cryptographic attack implementations — RSA, ECC, factorization, padding oracles, LCGs and more.

## What it's for

crypto-attacks (by Joachim Vandersmissen) is a collection of Python implementations of cryptographic attacks and utilities, run with SageMath and PyCryptodome. The catalog is broad: RSA (Wiener, Boneh-Durfee, Bleichenbacher, common modulus, ROCA, partial key exposure, ...), factorization (Fermat, branch-and-prune, Coppersmith, ...), ECC (MOV, Frey-Rück, Smart's attack, ECDSA nonce reuse, parameter recovery), CBC/CTR/GCM/IGE mode attacks (padding oracles, bit flipping, CRIME, forbidden attack), LCG and truncated-LCG recovery, Mersenne Twister state recovery, LWE, hidden number problem, knapsack and RC4 FMS. It is a library rather than a turn-key CLI: you pick the attack module, append your parameters to it (or import it from your own code), and execute with `sage -python`. The tests in `test/` exercise every attack with known-answer parameters and double as a cookbook of worked examples.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Check the SageMath environment

```
sage -python --version
```

Outcome: prints the Python version bundled with the local SageMath installation; the library expects Python 3.9 or newer, and older versions may break some scripts.

### Discover which attacks are implemented

```
ls attacks/rsa/ attacks/ecc/ attacks/factorization/
```

Outcome: lists the attack modules grouped by category (e.g. `boneh_durfee.py`, `wiener_attack.py` under `attacks/rsa/`), one named attack per file with its parameters documented in the module docstring.

### Run a specific attack against your parameters

```
import logging
logging.basicConfig(level=logging.DEBUG)
N = 88320836926176610260238895174120738360949322009576866758081671082752401596826820274141832913391890604999466444724537056453777218596634375604879123818123658076245218807184443147162102569631427096787406420042132112746340310992380094474893565028303466135529032341382899333117011402408049370805729286122880037249
e = 36224751658507610673165956970793195381480143363550601971796688201449789736497322700382657163240771111376677180786660893671085854060092736865293791299460933460067267613023891500397200389824179925263846148644777638774319680682025117466596019474987378275216579013846855328009375540444176771945272078755317168511
p, q = attack(N, e, 512, delta=0.26, m=3)
assert p * q == N

sage -python attacks/rsa/boneh_durfee.py
```

Outcome: with those lines appended to the bottom of `attacks/rsa/boneh_durfee.py`, Sage executes the attack, logging each lattice attempt (`Trying m = 3, t = 1...`, shift generation, LLL reduction) and printing the recovered factors `p` and `q`; if the lattice is too small the attack returns None, so wrap the call in a loop that increments `m` until factors are found.

### Exercise the whole library with its test suite

```
sage -python -m pytest test/
```

Outcome: runs every implemented attack against its known-answer vectors (a few minutes on a typical machine); failures usually point at a broken environment — SageMath/Python version skew or a missing dependency — rather than at the mathematics.
