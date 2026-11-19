---
name: "graphw00f"
categories:
  - blackarch-webapp
version: "1.2.1.r4.g4901f82"
homepage: "https://github.com/dolevf/graphw00f"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-05-31"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "dolevf/graphw00f"
github-stars: 916
github-pushed: "2026-05-16"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# graphw00f

> Detects and fingerprints the GraphQL engine behind an endpoint, then maps it onto the GraphQL Threat Matrix.

## What it's for

graphw00f (in the lineage of wafw00f) detects and fingerprints GraphQL servers: it probes with a mix of benign and malformed queries whose responses differ per implementation, identifying the engine — Graphene, Apollo, gqlgen, Hasura, graphql-java, WPGraphQL and dozens more — on both GET- and POST-style endpoints. A successful fingerprint is then mapped onto the GraphQL Threat Matrix, summarizing which security features the technology ships, their default on/off state and known CVEs, which turns a fingerprint into a targeted test plan. All probes are read-only; it does not attack the endpoint.

## Typical use cases

### Fingerprint an endpoint whose location is known

```
graphw00f -f -t https://target.example.com/graphql
```

Outcome: confirms GraphQL is live at the path, then prints `Discovered GraphQL Engine: (name)` with the technology stack, engine homepage and a link to that engine's threat-matrix document.

### Locate the endpoint first, then fingerprint

```
graphw00f -f -d -t https://target.example.com
```

Outcome: detection mode probes common GraphQL paths (or a custom list via `--wordlist`) until one responds, then fingerprints the discovered endpoint in the same run.

### List detectable engines and export results

```
graphw00f -l
graphw00f -f -t https://target.example.com/graphql -o results.csv
```

Outcome: `-l` prints every technology graphw00f can currently identify; `-o` writes the fingerprint result as a CSV row, useful when rolling up a whole scope.

No corpus evidence — derived from upstream documentation.
