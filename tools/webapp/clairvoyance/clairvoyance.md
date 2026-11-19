---
name: "clairvoyance"
categories:
  - blackarch-webapp
version: "2.5.2"
homepage: "https://github.com/nikitastupin/clairvoyance"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-01-12"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "nikitastupin/clairvoyance"
github-stars: 1554
github-pushed: "2025-12-05"
github-archived: false
github-issues: 42
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# clairvoyance

> Recover a GraphQL API schema by brute force when introspection is disabled.

## What it's for

Many GraphQL deployments turn off introspection in production, which normally blinds tooling that relies on `__schema`. Clairvoyance works around this: it sends probe documents and mines the server's validation error messages to infer type, field and argument names, iterating until it reconstructs a full schema in JSON. The output feeds directly into tools like GraphQL Voyager (visualization), InQL (Burp) or graphql-path-enum. Wordlist quality drives coverage — a GraphQL-specific wordlist finds far more than generic English lists. It does not attack the API; it only recovers its shape.

## Typical use cases

### Recover a schema from an endpoint with introspection disabled

Reconstruct the schema with the built-in wordlist and write it to a file.

```
clairvoyance https://target.example.com/graphql -o schema.json
```

Outcome: runs iterative blind introspection against the endpoint (expect minutes, not seconds), logging each iteration's progress, and writes the recovered schema as JSON to `schema.json` — ready to load into GraphQL Voyager or InQL.

### Improve field coverage with a custom wordlist

Supply GraphQL-flavored candidate names and authenticate if the endpoint requires it.

```
clairvoyance https://target.example.com/graphql -w graphql-wordlist.txt -H "Authorization: Bearer <token>" -o schema.json
```

Outcome: uses your wordlist (one candidate name per line) for all brute-force rounds instead of the bundled default, sends every request with the extra header, and writes the resulting schema to `schema.json`. More candidate names that match real fields means a more complete recovered schema.

### Resume from a partial schema through a proxy

Feed back a previously obtained schema and route requests through an intercepting proxy to watch the probes.

```
clairvoyance https://target.example.com/graphql -i schema.json -x http://127.0.0.1:8080 -o schema-full.json
```

Outcome: supplements the input JSON schema with newly discovered types instead of starting from zero, mirrors every probe request into the proxy for inspection, and writes the merged result to `schema-full.json`.

No corpus evidence — derived from upstream documentation.
