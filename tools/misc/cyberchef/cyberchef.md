---
name: "cyberchef"
categories:
  - misc
version: "10.4.0-0parrot1"
homepage: "https://github.com/gchq/CyberChef"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot, nixpkgs]
build-system: scripts
meta-checked: "2026-11-09"
github-repo: "gchq/CyberChef"
github-stars: 36337
github-pushed: "2026-11-07"
github-archived: false
github-issues: 591
github-match: "exact"
gh-checked: "2026-11-07"
worth: 9.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 361
gh-activity-checked: "2026-11-09"
---

# cyberchef

> Browser-based "cyber swiss army knife": chain encode/decode, encrypt, hash, compress and parse operations on any data without writing code.

## What it's for

CyberChef (GCHQ) is a web application for analysing and transforming data: drag
operations — simple encodings like Base64, hex and URL encoding, encryption like
XOR/AES/DES, hashing, compression, hexdumps, IPv6 and X.509 parsing — into a
recipe, paste input, and read the output. Recipes run entirely in the browser,
stack arbitrarily, and every intermediate result is visible, which makes it the
standard scratchpad for decoding blobs found in cookies, tokens, configs and
traffic captures. It also runs locally so no data ever leaves the host, and a
recipe can be packed into a URL that reproduces the whole setup when shared. It
performs no scanning and talks to no services — it operates only on the data you
give it.

## Typical use cases

### Run a private local instance

Keep sensitive data off the public hosted instance.

```
docker run -it -p 8080:8080 ghcr.io/gchq/cyberchef:latest
```

Outcome: serves CyberChef on http://localhost:8080 for use in a browser; all
parsing and transformation still happen client-side, so payloads never leave the
host.

### Decode a base64 blob and keep chaining

Peel layered encodings with stacked operations.

```
https://gchq.github.io/CyberChef/#recipe=From_Base64('A-Za-z0-9%2B%2F%3D',true,false)&input=aGVsbG8gd29ybGQ
```

Outcome: opens the app with From Base64 loaded and the input decoded in the
Output pane; click further operations (to hexdump, gunzip, decode again) onto the
recipe to strip additional layers, watching each step's output.

### Decrypt data with a known XOR key

Combine base64 decoding with XOR using a key recovered from source or config.

```
https://gchq.github.io/CyberChef/#recipe=From_Base64('A-Za-z0-9%2B%2F%3D',true,false)XOR('secret','UTF8',false)
```

Outcome: the Output pane shows the payload XOR-decrypted with the key
`secret` (interpreted as UTF-8); readable text confirms the key, garbled output
means the key or scheme is wrong.

### Recover an unknown XOR key by brute force

Try every single-byte key when the key itself is unknown.

```
https://gchq.github.io/CyberChef/#recipe=XOR_Brute_Force(1,100,0,'Standard',false,true)
```

Outcome: lists each candidate key (key length 1, sampled against the first 100
bytes) beside its decoded output; the row containing readable plaintext or a
known magic header identifies the key.

### Encode a payload for injection

Prepare shellcode, URLs or headers with proper escaping.

```
https://gchq.github.io/CyberChef/#recipe=URL_Encode(true)
```

Outcome: output is the input URL-encoded with all special characters escaped
(set the argument false to encode only reserved characters) — ready to paste into
a query string or header where raw metacharacters would break the request.

### Share or reproduce a transformation as a link

Hand an exact recipe to a colleague or your future self.

```
https://gchq.github.io/CyberChef/#recipe=To_Base64('A-Za-z0-9%2B%2F%3D')&input=dXNlci5leGFtcGxl
```

Outcome: the URL fragment carries the recipe and (Base64-encoded) input; anyone
opening it lands in CyberChef with the same operations, arguments and input
loaded — the built-in link buttons generate these URLs for you.
