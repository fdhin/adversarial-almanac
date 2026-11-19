---
name: "factordb-pycli"
categories:
  - blackarch-crypto
version: "1.3.0"
homepage: "https://github.com/ryosan-470/factordb-pycli"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "ryosan-470/factordb-pycli"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-09"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-stars: 56
github-pushed: "2023-04-10"
github-archived: false
github-issues: 5
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# factordb-pycli

> CLI and Python client for the factordb.com integer factorization database.

## What it's for

factordb-pycli queries factordb.com — a public database of known integer factorizations — from the command line (`factordb`) and from Python (`factordb.factordb.FactorDB`). In cryptanalysis work (typically RSA triage) it answers the first question about any modulus instantly: is it already factored? A status of `FF` means fully factored, so an RSA modulus with that status is broken by lookup and no computation is needed; other statuses indicate partial or no known factorization. It is a lookup client only — it reports what the database already holds, and it needs network access to factordb.com.

## Typical use cases

### Factor a number from the command line

```
factordb 16
```

Outcome: prints the known prime factors on one line (`2 2 2 2`), pulled live from factordb.com.

### Get status and factors as JSON

```
factordb --json 16
```

Outcome: prints `{"id": "http://factordb.com/?id=2", "status": "FF", "factors": [2, 2, 2, 2]}` — check `status` first: `FF` fully factored, other values mean the factorization is partial or unknown.

### Query the database from Python

```
python3 -c "from factordb.factordb import FactorDB; f = FactorDB(16); f.connect(); print(f.get_factor_list()); print(f.get_status())"
```

Outcome: `[2, 2, 2, 2]` and `FF` — `connect()` performs the HTTP request, then `get_factor_list()`, `get_status()` and `get_factor_from_api()` expose the response for use in scripts.

*No corpus evidence — derived from upstream documentation.*
