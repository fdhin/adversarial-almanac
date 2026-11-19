---
name: "hash-extender"
categories:
  - blackarch-crypto
version: "161.f00b1a0"
homepage: "https://github.com/iagox86/hash_extender"
packaged-by: [blackarch]
blackarch-builddate: "2025-02-01"
build-system: make
license: BSD
meta-checked: "2026-11-09"
github-repo: "iagox86/hash_extender"
github-stars: 1213
github-pushed: "2025-01-27"
github-archived: false
github-issues: 11
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# hash-extender

> Ron Bowes' hash length extension attack tool — forge a valid signature for appended data without knowing the secret key.

## What it's for

When an app signs `secret + data` with a Merkle–Damgård hash (MD4, MD5, RIPEMD-160, SHA-0, SHA-1, SHA-256, SHA-512, WHIRLPOOL) and hands you both `data` and the signature, hash-extender computes a signature for `secret + data + attacker_data` without knowing the secret — you only guess its length. It emits the new signature plus the exact padded data string to submit back to the app. It is an offensive crypto tool for testing flawed MAC/signature schemes; it does not crack hashes and cannot help against HMAC, which is immune by design.

## Typical use cases

### Forge a signature with a known secret length

Extend `data` with `append` and produce the matching signature.

```
hash-extender --data data --secret 6 --append append --signature 6036708eba0d11f6ef8b74d5b --format md5
```

Outcome: prints the type, secret length, the new signature (`6ee582a1669ce442f3719c47430dadee`) and the new string in hex — that string, sent as the data with the new signature, validates on the server.

### Try every matching algorithm when the type is unknown

Omit `--format` to attack all algorithms whose digest size fits the signature.

```
hash-extender --data data --secret 6 --append append --signature 6036708eba0d11f6ef8b74d5b --out-data-format html --table
```

Outcome: one table row per candidate algorithm (md4, md5, ripemd160, sha1, ...) with its new signature and appended string side by side, so each can be replayed until one is accepted.

### Sweep a range of unknown key lengths

Let the tool iterate when the secret length is only bounded.

```
hash-extender --data data --secret-min 8 --secret-max 32 --append append --signature 6036708eba0d11f6ef8b74d5b --format md5 --table
```

Outcome: one result block (or table row) per candidate key length from 8 to 32 — replay each new signature/data pair until the server accepts one, which also leaks the real key length.

### Emit the appended data in an encoding the target accepts

Match the output encoding to where the string is going (URL parameter, C string, raw bytes).

```
hash-extender --data data --secret 6 --append append --signature 6036708eba0d11f6ef8b74d5b --format md5 --out-data-format html
```

Outcome: the new string is rendered with `%NN` HTML/URL escapes instead of hex (other choices: raw, cstr `\xNN`, fancy), ready to paste into a request; input formats are selectable separately via `--data-format`.

*No corpus evidence — derived from upstream documentation.*
