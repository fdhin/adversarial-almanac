---
name: "flask-unsign"
categories:
  - blackarch-cracker
version: "v1.2.1.r0.g6e6e91d"
homepage: "https://github.com/Paradoxis/Flask-Unsign"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-01-12"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "Paradoxis/Flask-Unsign"
github-stars: 665
github-pushed: "2024-12-03"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# flask-unsign

> Decodes Flask session cookies, brute-forces the signing secret key from a captured cookie, and forges new signed cookies with the recovered key.

## What it's for

Flask session cookies are signed, not encrypted, so their payload is readable by anyone — and only the server's secret key keeps them tamper-proof. This tool attacks both halves: it decodes a cookie's payload locally with no key at all, brute-forces the secret key by signing the cookie with candidate secrets from a wordlist until the signature matches, and — once the key is known — signs arbitrary session payloads that the target server will accept as authentic. It can also fetch a session cookie straight off a live URL instead of having you copy it from a proxy. Reach for it when a web app stores who-is-logged-in inside the Flask session, or when a leaked or guessable secret key should be turned into a working admin session. If a recovered key produces cookies the server rejects, the target may run an older `itsdangerous`; retry the signing with `--legacy` to match its timestamp algorithm.

## Typical use cases

### Decode a captured session cookie

```
flask-unsign --decode --cookie 'eyJsb2dnZWRfaW4iOmZhbHNlfQ.X7fK2g'
```

Outcome: prints the decoded session payload, e.g. `{'logged_in': False}`. No secret key is needed — the cookie's contents are only signed, and this just parses them.

### Fetch a session cookie straight from the app

```
flask-unsign --decode --server 'https://www.example.com/login'
```

Outcome: the tool requests the URL itself, prints the HTTP status and the `session` cookie value it received, then decodes it as above. Pick a URL that actually issues a session, and pass `--cookie-name` if the app renamed the cookie.

### Brute-force the secret key from a cookie

```
flask-unsign --unsign --cookie 'eyJsb2dnZWRfaW4iOmZhbHNlfQ.X7fK2g' --wordlist secrets.txt --threads 8
```

Outcome: prints the decoded session, then a brute-forcer progress banner; on success it reports the number of attempts and the secret key (e.g. `CHANGEME`). Without `--wordlist` it falls back to the bundled wordlist when the wordlist extra is installed. A weak or default secret falls in seconds; a strong one simply never matches.

### Forge a signed session cookie

```
flask-unsign --sign --cookie "{'logged_in': True, 'username': 'admin'}" --secret 'CHANGEME'
```

Outcome: prints a fully signed cookie string; pasting it as the `session` cookie in a browser or proxy makes the server trust the forged identity. Add `--legacy` if the target uses the older signature format, and `--salt`/`--cookie-name` to match apps that customized Flask's session defaults.
