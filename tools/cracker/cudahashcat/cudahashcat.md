---
name: "cudahashcat"
categories:
  - blackarch-cracker
version: "2.01"
homepage: "http://hashcat.net/oclhashcat/"
packaged-by: [blackarch]
blackarch-builddate: "2024-11-29"
build-system: scripts
license: custom
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# cudahashcat

> The NVIDIA build of oclHashcat v2.01 — GPU dictionary, rule, and mask cracking of 150+ hash formats; final release of the line that merged into hashcat 3.x.

## What it's for

In the 2.x era the GPU edition of hashcat shipped as two binaries per vendor: `oclHashcat` for AMD and `cudaHashcat` for NVIDIA (both packaged from the same oclHashcat sources — the sibling note is [[oclhashcat]]). The BlackArch `cudahashcat` command wraps `cudaHashcat64.bin`, which supports straight dictionary attacks with a GPGPU rule engine, pure brute-force masks, combination, and hybrid dict+mask / mask+dict modes over 150+ algorithms, from raw MD5/NTLM/SHA family through WPA/WPA2 handshakes, Office documents, and archives. It needs an NVIDIA card at least sm_20 with driver ForceWare 346.59 or newer. Development ended at v2.01 (December 2015): hashcat 3.0 merged both vendor builds into a single `hashcat` binary, so on a current system install modern hashcat instead — this package exists to reproduce or read legacy cracking workflows.

## Typical use cases

### Crack WPA/WPA2 handshakes with a wordlist

```
cudahashcat -m 2500 capture.hccap wordlist.dict
```

Outcome: GPU dictionary attack against the captured handshake (`-m 2500` expects hashcat's .hccap format, one network per file); a recovered passphrase is printed with the target's BSSID and stored in the pot file, so re-runs skip already-cracked networks.

### Apply best64 rules to a dictionary

```
cat wordlist.dict | cudahashcat -m 1000 ntlm.txt -r rules/best64.rule
```

Outcome: reads candidates from stdin and applies the 64 most productive mangling rules shipped in the package's `rules/` directory to each one, massively expanding coverage of an NTLM hash list without a larger wordlist.

### Combine mask and dictionary (hybrid attack)

```
cudahashcat -t 32 -a 7 example0.hash ?a?a?a?a example.dict
```

Outcome: attack mode 7 appends every 4-character combination (`?a` = all printable) to each word from example.dict — the pattern for password policies that demand trailing specials or digits; raise `-t` to grow the appended tail.

### Benchmark GPU cracking speed

```
cudahashcat --benchmark
```

Outcome: runs the built-in benchmark across all supported hash types and prints Mh/s tables per algorithm, which is how the tool's advertised rates and health of the driver setup are checked before a real run.

*No corpus evidence — derived from upstream documentation.*
