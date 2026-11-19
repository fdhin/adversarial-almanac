---
name: "ciphr"
categories:
  - blackarch-crypto
version: "127.5da7137"
homepage: "https://github.com/frohoff/ciphr"
packaged-by: [blackarch]
blackarch-builddate: "2025-04-29"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "frohoff/ciphr"
github-stars: 122
github-pushed: "2018-08-04"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ciphr

> Composable CLI pipeline for encoding, hashing, encrypting, and transforming data taken from literals, files, or stdin.

## What it's for

ciphr (a Ruby gem) runs a left-to-right pipeline of functions over a data stream: encodings (hex, base32, many base64 alphabets, radix 2–36), hashes (MD4 through SHA-512 plus HMAC variants), symmetric ciphers (AES, Blowfish, Camellia, CAST, DES/3DES, RC2, RC4), XOR and bitwise operations, and compression. Input comes from a quoted literal, `@file`, or stdin; the result goes to stdout. Prefixing a function with `~` inverts it (decode/decrypt), which makes undo chains natural. The author positions it as an experimental tool for quick data manipulation in forensics and penetration testing — its crypto is explicitly not hardened (e.g. against timing attacks), so it must not back any online security mechanism.

## Typical use cases

### Encode or hash a literal or file

```
ciphr '"abc"' hex
ciphr @invoice.pdf sha256 hex
```

Outcome: the first prints `616263`; the second prints the SHA-256 hex digest of the file's bytes. Literals go in quotes, `@path` reads a file, and each further pipeline word is applied to the previous stage's output.

### Decode with the inverse operator

```
ciphr '"616263"' ~hex
```

Outcome: prints `abc`. `~` runs a function backwards, so any forward chain is undone by replaying it with the functions inverted.

### XOR data with a repeating key

```
echo -n "abc" | ciphr xor[0x01] hex
```

Outcome: XORs each stdin byte with key byte `01` and prints the result as hex (`606362`). Keys take string (`"key"`), hex (`0x020304`), or file (`@keyfile`) form; multi-byte keys repeat over the stream.

### Encrypt and decrypt with a symmetric cipher

```
echo -n "handoff note" | ciphr 'aes-256-cbc["Password1"] hex' > note.enc
ciphr @note.enc '~hex ~aes-256-cbc["Password1"]'
```

Outcome: the first pipeline encrypts stdin with AES-256-CBC under the bracketed key and hex-encodes the ciphertext into `note.enc`; the second reverses both stages in order and prints the plaintext. Without `hex`/`~hex` the ciphertext stage is raw binary, so redirect it to a file.

*No corpus evidence — derived from upstream documentation.*
