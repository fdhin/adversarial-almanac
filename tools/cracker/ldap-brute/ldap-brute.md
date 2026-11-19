---
name: "ldap-brute"
categories:
  - blackarch-cracker
version: "21.acc06e3"
homepage: "https://github.com/droope/ldap-brute"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "droope/ldap-brute"
github-stars: 14
github-pushed: "2013-12-04"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ldap-brute

> Exploits LDAP injection in a web parameter to brute-force hidden values — usernames, attribute values, attribute names — over HTTP.

## What it's for

ldap-brute (entry script `ldap_brute.py`, Python 2) targets web applications whose HTTP parameters are concatenated into LDAP filters without escaping. You supply a vulnerable URL containing a `%s` placeholder at the injection point and a "true string" that only appears in the response when the injected filter evaluates true; the tool then walks wildcard prefixes or character combinations to recover every valid value the directory will confirm. It never talks to the LDAP server directly and does not crack passwords — it only extracts data the vulnerable application will vouch for. Non-wildcard mode exists because some LDAP attributes (numeric IDs, DNs) do not honor `*`.

## Typical use cases

### Enumerate valid usernames through a wildcard injection

Point the tool at a URL where `%s` sits inside a filter that returns the true-string only for existing entries.

```
python2 ldap_brute.py 'http://target.example.com/ldap/query.php?name=%s)(cn=*))%%00&password=' 'AUTHENTICATED as'
```

Outcome: walks `a*`, `b*`, ... prefixes over HTTP, narrowing to complete valid values (e.g. `admin`, `hacker`) and prints each one plus a summary with total time and HTTP request count. Note the doubled `%%` — the URL is a printf-style format string.

### Brute-force values of a non-wildcard attribute

For attributes that ignore wildcards (e.g. numeric identifiers), generate value combinations instead.

```
python2 ldap_brute.py --no-wildcard -a gidNumber -c digits --max-word-size 5 'http://target.example.com/ldap/query.php?name=admin)%s)%%00&password=' 'AUTHENTICATED as'
```

Outcome: builds large always-true filters embedding candidate values (digits only here, up to 5 characters) and reports each value the server accepts; much slower than wildcard mode.

### Discover valid attribute names

Brute-force the LDAP schema itself rather than a value.

```
python2 ldap_brute.py -A -c lower --max-word-size=4 'http://target.example.com/ldap/query.php?name=admin)%s)%%00&password=' 'AUTHENTICATED as'
```

Outcome: recovers short lowercase attribute names the filter accepts; `-w wordlists/attribute_names` swaps generated candidates for a wordlist. Lowercase-only is recommended — mixing digits can synthesize invalid attribute names.

*No corpus evidence — derived from upstream documentation.*
