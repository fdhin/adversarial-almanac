---
name: "interrogate"
categories:
  - blackarch-forensic
version: "5.eb5f071"
homepage: "https://github.com/carmaa/interrogate"
packaged-by: [blackarch]
blackarch-builddate: "2019-10-10"
build-system: make
license: GPL3
meta-checked: "2026-11-09"
github-repo: "carmaa/interrogate"
github-stars: 77
github-pushed: "2019-10-06"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# interrogate

> Entropy-scanning proof of concept that locates cryptographic keys (AES, Serpent, Twofish, RSA) in memory dumps and other binary material.

## What it's for

Written by Carsten Maartmann-Moe (carmaa) as part of his NTNU master's thesis on forensic key identification. It slides a window over binary data, measures entropy per window, and reports candidate key material with its offset — regardless of which operating system produced the dump. Version 0.0.4 identifies AES (128/192/256-bit), Serpent and Twofish (256-bit), DER-encoded RSA keys, and Windows CryptoAPI RSA private key blobs. It can also reconstruct a process's virtual address space from a page-directory-base (CR3/PDB) offset before searching. It only finds candidate keys — it does not decrypt volumes or crack passwords, and the author bills it as a proof of concept.

## Typical use cases

### Scan a memory dump for all supported key types

Default run with no key type or size restrictions.

```
interrogate memdump.bin
```

Outcome: candidate keys are reported as they are found, each with the algorithm, key length and file offset; low-quality candidates can be pruned by raising the entropy threshold with `-t`.

### Hunt for AES keys of a specific length

Restrict the search to one algorithm and key size.

```
interrogate -a aes -k 256 memdump.bin
```

Outcome: only AES-256 candidates are reported; RSA keys are found regardless of length while Serpent/Twofish require 256-bit keys.

### Locate RSA keys, including Windows CryptoAPI blobs

Search for DER-encoded RSA keys or Windows Private Key BLOB structures.

```
interrogate -a win-rsa memory.dmp
```

Outcome: candidate RSA private key blobs found in the dump are printed with their offsets, ready to be extracted for offline use.

### Limit the search to a byte interval

Skip regions already ruled out or focus on a heap segment.

```
interrogate -i 1000000:1ffffff memdump.bin
```

Outcome: only the given hexadecimal offset range is scanned; omitting either side of `from:to` means start or end of file.

### Reconstruct a process address space, then search it

Rebuild paged-out virtual memory around a known process before key hunting.

```
interrogate -r 1d2e3000 memdump.bin
```

Outcome: the virtual address space for the process at the given CR3/PDB offset (findable with a PTFinder-style scanner or Volatility) is reconstructed into a `pages` file, which is then searched for keys; combine with `-i` to bound the virtual range.

*No corpus evidence — derived from upstream documentation.*
