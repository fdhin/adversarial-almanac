---
name: "flask-session-cookie-manager3"
categories:
  - blackarch-webapp
version: "v1.2.2.r1.g3c271d2"
homepage: "https://noraj.github.io/flask-session-cookie-manager/"
packaged-by: [blackarch]
blackarch-builddate: "2025-02-08"
build-system: scripts
license: MIT
aliases: ["flask-session-cookie-manager2"]
meta-checked: "2026-11-09"
github-repo: "noraj/flask-session-cookie-manager"
worth: 4.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-stars: 771
github-pushed: "2025-02-08"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# flask-session-cookie-manager3

> Decodes Flask session cookies — and forges them when the secret key is known.

## What it's for

Flask stores its session client-side in the `session` cookie, signed with the application's secret key via itsdangerous. This tool (maintained by Alexandre ZANNI / noraj, from Wilson Sumanang's original decoder) decodes that cookie's payload with or without the key, and — when you have or crack the secret — re-encodes an arbitrary session structure into a validly signed cookie. That pair is the standard triage flow for Flask targets: read what the application keeps in the session, then test whether a weak secret lets you forge an escalated one. The `3` suffix marks the Python 3 command; `flask-session-cookie-manager2` is the Python 2 twin with identical options.

## Typical use cases

### Decode a session cookie without the key

Inspect what the application stores client-side.

```
flask-session-cookie-manager3 decode -c 'eyJudW1iZXIiOiIzMjY0MTAwMzE1MDUiLCJ1c2VybmFtZSI6ImFkbWluIn0.X'
```

Outcome: prints the cookie's payload dictionary with the values still base64-encoded — enough to see the session field names and structure without knowing the key.

### Decode with the secret key

Verify a guessed or cracked secret.

```
flask-session-cookie-manager3 decode -c 'eyJudW1iZXIiOiIzMjY0MTAwMzE1MDUiLCJ1c2VybmFtZSI6ImFkbWluIn0.X' -s '.{y]tR&sp&77RdO~u3@XAh#TalD@Oh~yOF_51H(QV};K|ghT^d'
```

Outcome: with the correct key the signature is verified and the plaintext session structure is printed (e.g. `{"username": "admin", "number": "326410031505"}`); a wrong key fails the check.

### Forge a session with a known key

Escalate privileges inside a signed session.

```
flask-session-cookie-manager3 encode -s '.{y]tR&sp&77RdO~u3@XAh#TalD@Oh~yOF_51H(QV};K|ghT^d' -t '{"username":"admin","number":"326410031505"}'
```

Outcome: emits a validly signed session cookie string for the supplied dictionary (which must be a valid Python dictionary/JSON); setting it as the `session` cookie in a browser tests whether the application trusts forged sessions.
