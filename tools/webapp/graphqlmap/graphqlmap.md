---
name: "graphqlmap"
categories:
  - blackarch-webapp
version: "63.59305d7"
homepage: "https://github.com/swisskyrepo/GraphQLmap"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-01-12"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "swisskyrepo/GraphQLmap"
github-stars: 1696
github-pushed: "2024-03-11"
github-archived: false
github-issues: 20
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# graphqlmap

> Interactive GraphQL pentesting console: schema dumping, field fuzzing, request batching and injection helpers.

## What it's for

GraphQLmap is a scripting engine for pentesting a GraphQL endpoint: connect with a URL, method, custom headers and proxy, then dump the schema via introspection (the dump also populates tab-autocomplete), execute arbitrary queries and mutations one line at a time, fuzz field values using `GRAPHQL_INCREMENT` and `GRAPHQL_CHARSET` placeholders, batch requests with `BATCHING_N`, and run semi-automatic blind NoSQL (`nosqli`) and SQL (`mysqli`, `postgresqli`, `mssqli`) attacks against a field. It is a hands-on console rather than a scanner — you drive each attack and it handles the query plumbing and the oracle loops.

## Typical use cases

### Connect to an endpoint and dump the schema

```
graphqlmap -u https://target.example.com/graphql -v --method POST
```

Outcome: opens the `GraphQLmap >` prompt against the endpoint; running `dump_new` there prints the full schema (fields, types, arguments) and loads the discovered names into autocomplete for the session.

### Send authenticated traffic through Burp

```
graphqlmap -u https://target.example.com/graphql --headers '{"Authorization": "Bearer <token>"}' --proxy http://127.0.0.1:8080
```

Outcome: every request the tool fires carries the supplied header and appears in Burp's history, so each generated query can be inspected and replayed by hand.

### Fuzz a field and run a blind injection from the prompt

Embed the placeholders in a query, then invoke an attack helper.

```
GraphQLmap > { paste(pId: "GRAPHQL_INCREMENT_10") {id,title,content} }
GraphQLmap > nosqli
```

Outcome: the first line iterates the numeric placeholder (0..9) and shows each response's status/length to reveal differences; `nosqli` then runs regex-based blind extraction against a query you supply (using `BLIND_PLACEHOLDER`), printing each extracted value such as a session identifier.

No corpus evidence — derived from upstream documentation.
