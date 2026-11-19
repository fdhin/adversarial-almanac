---
name: "sgn"
categories:
  - blackarch-binary
version: "42.8e2c4e1"
homepage: "https://github.com/EgeBalci/sgn"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-05-02"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "EgeBalci/sgn"
github-stars: 2007
github-pushed: "2026-07-28"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 7
gh-activity-checked: "2026-11-09"
---

# sgn

> Polymorphic shellcode encoder — a modern reimplementation of Shikata ga nai.

## What it's for

sgn encodes raw position-independent shellcode so the result is
statistically close to random bytes: each pass ciphers the payload with an
additive feedback loop (an LFSR-style key stream), prepends a decoder stub
with no visible loop condition, pads with random value-preserving garbage
instructions, and — unless disabled — encrypts the decoder stub itself with a
per-run random schema cipher so no fixed decoder signature remains. It is a
from-scratch reimplementation of Metasploit's classic Shikata ga nai encoder
adding x64 support, a 1-byte key, and the stub-encryption layer. Upstream has
been ported from Go to Rust (current releases build with plain `cargo`); the
BlackArch package tracks the tool's earlier Go lineage. It is an encoder for
payload-development research in a lab — it transforms bytes, it does not
deliver them.

## Typical use cases

### Encode a raw payload while avoiding bad characters

```
sgn -i shellcode.bin -o encoded.bin -a 64 --badchars '\x00\x0a\x0d'
```

Outcome: writes `encoded.bin` containing the self-decoding polymorphic
output; the encoder guarantees the forbidden bytes (`\x00`, `\x0a`, `\x0d`
here) do not appear, which is what makes the blob survivable through
string-copying delivery paths. When executed, the stub decodes the original
shellcode in memory and transfers execution to it.

### Apply multiple encoding passes

```
sgn -i shellcode.bin -o encoded.bin -a 64 -c 3
```

Outcome: re-encodes the payload three times, each pass with fresh keys,
garbage layout, and schema — three nested decoders must unwrap at runtime,
and each generated copy is a different byte sequence even from identical
input.

### Produce a fully printable payload

```
sgn -i shellcode.bin -o encoded.bin --ascii
```

Outcome: every byte of the output is ASCII-printable (the run is slower due
to retrying schemas until the constraint holds), enabling injection through
contexts that strip or corrupt non-printable bytes.

### Encode without clobbering registers

```
sgn -i shellcode.bin -o encoded.bin -S
```

Outcome: the encoder adds a register-save prefix and restore suffix so all
register values are preserved across decoding — for calling conventions or
harnesses where the payload is reached mid-function with live state.

*No corpus evidence — derived from upstream documentation.*
