---
name: "jwt-hack"
categories:
  - blackarch-webapp
version: "v2.6.0.r148.g9474bb7"
homepage: "https://github.com/hahwul/jwt-hack"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-10-31"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "hahwul/jwt-hack"
github-stars: 1090
github-pushed: "2026-11-01"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 372
gh-activity-checked: "2026-11-09"
---

# jwt-hack

> Rust toolkit for attacking JSON Web Tokens: decode/encode, signature verification, secret cracking, attack-payload generation, automated vulnerability scanning and a REST API mode.

## What it's for

jwt-hack condenses the whole JWT testing workflow into one fast CLI. It decodes and encodes tokens (including DEFLATE-compressed JWTs and JWE structures), verifies signatures with a secret or an asymmetric key, cracks HMAC secrets by dictionary or brute force, generates classic attack payloads (none-algorithm, algorithm confusion, jku/x5u header injection, kid/claim tampering), and runs an automated `scan` that checks a token for the common JWT weaknesses in one pass. It also runs as a local REST API (and even an MCP server for AI-agent integration) when you want JWT tooling behind HTTP instead of subprocesses. Use it whenever you hold a token and need to know what is in it, whether the secret protecting it is weak, and which confusion/header attacks it is exposed to.

## Typical use cases

### Decode a token

Inspect header and claims without verification.

```
jwt-hack decode eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0In0.abc123
```

Outcome: prints the decoded header and payload (algorithm, claims, issued-at/expiry check); DEFLATE-compressed tokens are detected and decompressed automatically, and five-part JWE tokens are shown structurally (header, encrypted key, IV, ciphertext, tag).

### Craft a token

Encode claims with a chosen secret, key or algorithm.

```
jwt-hack encode '{"sub":"1234","role":"admin"}' --secret=Password1
# asymmetric:
jwt-hack encode '{"a":"z"}' --private-key RS256.key --algorithm=RS256
```

Outcome: emits a signed JWT for the given JSON payload — `--secret` for HMAC (HS256/384/512), `--private-key`/`--algorithm` for RS256/ES256/EdDSA and friends; add `--compress` for a DEFLATE-compressed token or `--jwe` for an encrypted JWE.

### Verify a signature

Check whether a secret or key actually validates a token.

```
jwt-hack verify JWT_TOKEN --secret=Password1
# asymmetric algorithms:
jwt-hack verify JWT_TOKEN --private-key path/to/RS256_private.key
```

Outcome: reports whether the signature verifies with the given symmetric secret or private key — the quick way to confirm a cracked or guessed key before using it to mint tokens.

### Crack a weak secret

Dictionary and brute-force attacks on HMAC secrets.

```
jwt-hack crack -w wordlist.txt JWT_TOKEN
# brute force short secrets:
jwt-hack crack -m brute JWT_TOKEN --max=4
```

Outcome: `crack` tries every wordlist entry (or, in `-m brute` mode, every character combination up to `--max` length) as the signing secret and prints the secret on success; compressed JWTs are handled transparently.

### Audit a token in one pass

The automated vulnerability scanner.

```
jwt-hack scan JWT_TOKEN --skip-crack -w wordlist.txt
```

Outcome: prints a findings report covering none-algorithm acceptance, weak secrets (custom wordlist via `-w`, or `--skip-crack` to skip), RS256→HS256 algorithm confusion, missing/improper expiry and security claims, kid header injection potential, and jku/x5u URL-spoofing vectors — with `--skip-payloads` available to skip payload generation.

### Serve the toolkit over HTTP

Run it as a local REST API.

```
jwt-hack server --api-key Password1
curl -s http://127.0.0.1:3000/health -H 'X-API-KEY: Password1'
```

Outcome: an API server listens on localhost:3000 exposing decode/encode/verify/crack/payload endpoints (all requiring the `X-API-KEY` header when `--api-key` is set) — for pipelines and integrations that prefer HTTP to shelling out; `jwt-hack mcp` exposes the same functions over stdio for MCP-capable clients.

No corpus evidence — derived from upstream documentation (hahwul/jwt-hack README: Basic Usage, scan checks, server/MCP sections).
