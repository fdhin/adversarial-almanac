---
name: "treasure"
categories:
  - blackarch-recon
version: "2.b3249be"
homepage: "https://github.com/GuerrillaWarfare/Treasure"
packaged-by: [blackarch]
blackarch-builddate: "2019-08-24"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "GuerrillaWarfare/Treasure"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# treasure

> Hunt secrets through GitHub code search: interactive queries, users' public SSH keys, and token/IP/wallet extraction from saved code dumps (Python 2 era, upstream gone).

## What it's for

Treasure (GuerrillaWarfare) mined GitHub's code search for sensitive information: an interactive hunt mode plus targeted extractors for a user's public SSH keys and for secrets inside files you feed it — Instagram and Facebook access tokens, IPv4/IPv6 addresses, Bitcoin wallet addresses and blockchain identifiers. The original repository is gone (BlackArch now builds the package from its own mirror of the last source), the code is Python 2 against GitHub's pre-authentication code-search scraping interface, and that request path no longer exists in current GitHub — treat the tool as historical: the CLI surface below is verified from the packaged source, but a current GitHub account cannot drive it as-is. The extraction commands (`-f` against a local file) are the parts whose logic still applies to any saved code dump.

## Typical use cases

### Interactively search GitHub code

Open the guided search over GitHub code search.

```
treasure hunt
```

Outcome: enters the interactive code-search environment where queries return matching code from GitHub; findings are shown inline for triage. (Historical: relies on the removed unauthenticated search path.)

### Pull a GitHub user's public SSH keys

Recover the keys a user has published on GitHub.

```
treasure -n user ssh
```

Outcome: fetches and prints the public SSH key(s) registered by that GitHub username — usable for user attribution, key reuse checks or adding to a crack/audit list.

### Extract secrets from a saved code dump

Parse a file of scraped code for indicators.

```
treasure -f dump.txt ipv4
```

Outcome: extracts every IPv4 address found in dump.txt and prints them; swap the command for `ipv6`, `iat` (Instagram access tokens), `fat` (Facebook access tokens), `btc` (Bitcoin wallet addresses) or `bid` (blockchain identifiers) to target other secret types — useful for triaging bulk code before manual review.

*No corpus evidence — derived from upstream (packaged mirror) source.*
