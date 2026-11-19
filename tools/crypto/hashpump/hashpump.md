---
name: "hashpump"
categories:
  - blackarch-crypto
version: "49.314268e"
homepage: "https://github.com/bwall/HashPump"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2016-10-03"
build-system: make
license: MIT
meta-checked: "2026-11-09"
github-repo: "bwall/HashPump"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# hashpump

> bwall's hash length extension attacker — recompute `H(key + data + append)` signatures without knowing the key, from CLI or Python.

## What it's for

HashPump exploits the hash length extension attack against CRC32, MD5, SHA1, SHA256 and SHA512: when a service authenticates data as `hash(secret + data)` and exposes both the data and the digest, HashPump forges a digest for `secret + data + your_addition` given only the secret's length. It ships a `hashpump` CLI and an official Python binding (`hashpumpy` on PyPI) for scripting the attack, e.g. looping over candidate key lengths. Like all length-extension tooling it is useless against HMAC and only applies to vulnerable MAC/signature constructions. The upstream GitHub repository is gone; the BlackArch package and the archived README document this exact 1.2.0 CLI.

## Typical use cases

### Forge a signature from the CLI

Extend a known signed message and get the new digest plus the payload to submit.

```
hashpump -s 6d5f807e23db210bc254a28be2d6759a0f5f5d99 --data 'count=10&lat=37.351&user_id=1&long=-119.827&waffle=eggo' -a '&waffle=liege' -k 14
```

Outcome: prints the new signature (`0e41270260895979317fff3898ab85668953aaa2`) followed by the original data with the `\x80...` padding glue and the appended text — send that string with the new signature and the server validates it.

### Verify the build before trusting its output

Run the built-in algorithm self-tests.

```
hashpump -t
```

Outcome: each supported algorithm (CRC32, MD5, SHA1, SHA256, SHA512) is exercised against known vectors and pass/fail is reported — a sanity check that the compiled OpenSSL-backed build computes extensions correctly.

### Script the attack over candidate key lengths in Python

Use the `hashpumpy` binding when the key length is unknown and must be brute-forced per request.

```
python3 -c "
import hashpumpy
sig, msg = hashpumpy.hashpump('6d5f807e23db210bc254a28be2d6759a0f5f5d99',
                              'count=10&user_id=1&waffle=eggo',
                              '&waffle=liege', 14)
print(sig, msg)"
```

Outcome: `hashpumpy.hashpump(hexdigest, original_data, data_to_add, key_length)` returns a `(new_digest, new_message)` tuple — wrap it in a loop over key lengths and replay requests until the server accepts, which confirms the real key length.
