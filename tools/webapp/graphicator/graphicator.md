---
name: "graphicator"
categories:
  - webapp
homepage: "https://github.com/cybervelia/graphicator"
origin: "howtohunt"
recipe: "upstream"
packaged-by: []
build-system: python
license: MIT
meta-checked: "2026-11-13"
github-repo: "cybervelia/graphicator"
github-match: "exact"
github-stars: 133
github-pushed: "2023-01-29"
github-issues: 0
gh-checked: "2026-11-13"
worth: 4.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
---

# graphicator

> GraphQL enumeration and data-extraction tool: walks the introspection schema, auto-builds every valid query and dumps the responses.

## What it's for

Graphicator (Cybervelia) is a GraphQL "scraper"/extractor. It sends the
introspection query to a GraphQL endpoint, re-structures the returned schema
internally, re-creates the supported queries from it, then fires each query
at the endpoint and saves the responses — building an automatic dump of
everything the schema exposes (including fields an API never meant to hand
out, like password fields on user objects). Erroneous responses are not
saved, and both successes and errors are cached so re-runs skip
already-tested queries. Its hard dependency is introspection: if the target
disabled introspection, it has nothing to work from — schema-reconstruction
tools (e.g. Clairvoyance-class wordlist attacks) cover that case instead.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Dump a GraphQL endpoint's data

Introspect, generate every query, harvest the responses.

```
python3 graphicator.py --target https://target.example.com/graphql --verbose --multi
```

Outcome: introspection runs first, then each reconstructed query is sent
with progress on screen; responses land in `reqcache/` (JSON per query),
the raw introspection in `reqcache-intro/`, and the matching generated
query text in `reqcache-queries/` — filenames are query+URL hashes, so a
response and its query file pair up.

### Sweep many endpoints from a file

Cover a list of GraphQL endpoints in one run.

```
python3 graphicator.py --file targets.txt
```

Outcome: reads one GraphQL URL per line and processes each in turn;
`--target` may also be repeated on the command line for a handful of hosts.

### Scan an authenticated endpoint

Reach endpoints behind API keys or auth headers.

```
python3 graphicator.py --target https://target.example.com/graphql --header "x-api-key: ***"
```

Outcome: the header is attached to every introspection and data request, so
the harvested responses reflect the authenticated role's reach — repeat per
role to compare exposure. `--insecure` skips self-signed-certificate
complaints, `--no-cache` forces full re-retrieval.

### Route the traffic through Burp or Tor

Keep every generated request inspectable.

```
python3 graphicator.py --target https://target.example.com/graphql --default-burp-proxy
```

Outcome: all requests go through the default Burp listener
(127.0.0.1:8080) for manual review; `--use-proxy` takes any custom proxy
and `--use-tor` routes via the local Tor SOCKS listener instead.
