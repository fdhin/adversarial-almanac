---
name: "hlextend"
categories:
  - blackarch-crypto
version: "17.be21920"
homepage: "https://github.com/stephenbradshaw/hlextend"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "stephenbradshaw/hlextend"
github-stars: 137
github-pushed: "2023-01-28"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# hlextend

> Pure Python hash length extension module for SHA1, SHA256 and SHA512.

## What it's for

hlextend is a single-file Python library that performs hash length extension attacks against the Merke-Damgard hash functions SHA1, SHA256 and SHA512. Given a digest of `secret || known_data`, where the secret itself is unknown but its length is known or guessable, it computes both the glue-padding string to append and the valid digest of `secret || known_data || append_data` — without ever learning the secret. The classic target is a MAC or request signature of the form `hash(secret + message)`, where an attacker wants the server to accept extra data appended to the message. It is a library, not a command-line program: it is used from Python scripts or an interactive interpreter.

## Typical use cases

### Forge a hash with appended data when the secret length is known

```
python3 -c "import hlextend; sha = hlextend.new('sha1'); print(sha.extend(b'file', b'hello', 10, '52e98441017043eee154a6d1af98c5e0efab055c')); print(sha.hexdigest())"
```

Outcome: prints the bytestring to submit as the new message (`b'hello\x80\x00...\x00xfile'`, padding plus `file`) and the forged SHA1 digest `c60fa7de0860d4048a3bfb36b70299a95e6587c9` that the signer will compute over `secret || hello || padding || file` for any 10-byte secret that produced the original hash.

### Brute-force an unknown secret length

```
python3 -c "
import hlextend
for secret_len in range(1, 65):
    sha = hlextend.new('sha256')
    sha.extend(b'admin=true', b'data', secret_len, 'ORIGINAL_SHA256_HEX')
    print(secret_len, sha.hexdigest())"
```

Outcome: prints one candidate digest per secret length; replay each against the target service until one is accepted, which also reveals the secret's length.

### Work with a different hash family via the named constructors

```
python3 -c "import hlextend; sha = hlextend.sha512(); print(sha.extend(b'x', b'y', 16, 'ORIGINAL_SHA512_HEX')); print(sha.hexdigest())"
```

Outcome: returns the padding-plus-append payload and forged digest for a SHA512-based MAC — `sha1()`, `sha256()` and `sha512()` are shortcuts for `new(algorithm)`.

*No corpus evidence — derived from upstream documentation.*
