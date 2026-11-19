---
name: "graphql-path-enum"
categories:
  - blackarch-webapp
version: "23.5450280"
homepage: "https://gitlab.com/dee-see/graphql-path-enum"
packaged-by: [blackarch]
blackarch-builddate: "2024-08-31"
build-system: go
license: Unlicense
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# graphql-path-enum

> Lists every practical path through a GraphQL schema that reaches a given type, from saved introspection data.

## What it's for

graphql-path-enum answers one question: given a GraphQL schema as an introspection query result saved to JSON, what are the different field paths that lead from the root to a particular type? Authorization is usually enforced per field or per path, so enumerating all reachable routes to a sensitive type (say `User`) is how you find the path nobody locked down. Graphs contain loops, so the listing is not literally exhaustive of an infinite path space, but it is relatively exhaustive; relay-style connection nodes are skipped by default and mutation-rooted paths are opt-in because they add noise. It is a Rust binary whose input is data you export from the target's own introspection endpoint.

## Typical use cases

### Enumerate paths to a sensitive type

```
graphql-path-enum -i introspection.json -t User
```

Outcome: prints `Found N ways to reach the "User" node from the "Query" node:` followed by each path as a chain of `Type (field)` hops — audit the authorization on every field that appears.

### Expand connections and include mutation paths

```
graphql-path-enum -i introspection.json -t User --expand-connections --include-mutations
```

Outcome: the same listing additionally expanded through pagination wrappers (edges, nodes, pageInfo) and paths rooted at the Mutation type, at the cost of a much longer output.

No corpus evidence — derived from upstream documentation.
