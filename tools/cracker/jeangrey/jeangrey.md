---
name: "jeangrey"
categories:
  - blackarch-cracker
version: "43.9bf3cf4"
homepage: "https://github.com/SideChannelMarvels/JeanGrey"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "SideChannelMarvels/JeanGrey"
github-stars: 373
github-pushed: "2023-10-13"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# jeangrey

> Differential fault analysis (DFA) toolkit for AES and SM4, centred on phoenixAES.

## What it's for

Recovers AES and SM4 keys from white-box implementations using differential
fault analysis: you record correct and faulty outputs (obtained by glitching
or patching the white-box) and the tools solve for the last-round key. The
repository ships phoenixAES (the Piret-Quisquater attack on AES-128, covering
faults injected in round 9 and round 8), a phoenixAES-yifan variant that tries
all ciphertext combinations when phoenixAES fails, phoenixSM4, and
aes-known-key-fault-analysis for locating an injected fault once the key is
known. The packaged `jeangrey` package installs the phoenixAES Python library —
there is no `jeangrey` binary; the attack is driven through its small Python
API, and the trace file's first line must always be the correct (unfaulted)
output.

## Typical use cases

### Recover the AES last-round key from faulted outputs

Feed a trace file of correct-plus-faulty ciphertexts to the round-9 attack.

```
python3 -c "import phoenixAES; phoenixAES.crack_file('tracefile')"
```

Outcome: with the reference output on the first line and at least 4x2
exploitable single faults from round 9, prints the recovered last-round key
(`Last round key #N found: <hex>`) in well under a second; the sibling tools
in the repository then derive the full AES key from it.

### Attack round-8 faults

Convert two round-8 faulty outputs into the round-9 setting and solve.

```
python3 -c "import phoenixAES; phoenixAES.convert_r8faults_file('r8faults','r9faults'); phoenixAES.crack_file('r9faults')"
```

Outcome: `convert_r8faults_file` rewrites the round-8 faulty ciphertext pair
into equivalent round-9 faults written to `r9faults`, and the subsequent
`crack_file` run recovers the same last-round key as the direct round-9
attack.
