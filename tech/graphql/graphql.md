---
technology: GraphQL
kind: overview
audience: offensive-security
layer: api-query-language
first-released: 2015
homepage: "https://graphql.org"
github-repo: "graphql/graphql"
source: "rewritten from HackTricks (network-services-pentesting/pentesting-web/graphql.md), CC BY-NC 4.0, accessed 2026-11-12"
provenance: "hybrid: corpus-grounded (1 ledger rows, internal coverage ledger) + HackTricks enrichment 2026-11-13; credit in source line"
---

# GraphQL

> One endpoint, one POST body, the whole data graph — and out of the box most servers will happily describe every field, argument, and mutation they have. Directly to you.

## What it is

GraphQL is a query language plus a server runtime for APIs, born inside Facebook around 2011 to fix mobile clients under- and over-fetching from a wall of News Feed endpoints, open-sourced in 2015, and stewarded today by the GraphQL Foundation. It is a specification, not a framework: Apollo Server and graphql-js (JavaScript), graphql-java, GraphQL Ruby, Graphene/Strawberry/Ariadne (Python), HotChocolate (.NET) and gql-gen (Go) all speak the same wire contract — so the wire behavior is strikingly consistent across targets even when the code behind it is not.

The mental model in one paragraph: the client ships a single document that mirrors the exact JSON it wants back; the server validates that document against its schema, walks it as a tree, calling a developer-written **resolver** function for every field, and stitches the results into a response shaped exactly like the request. Reads (queries), writes (mutations), and pushes (subscriptions) all multiplex through one URL — almost always `POST /graphql`.

The operator takeaway is the collision of two design decisions. The protocol is **self-describing** — introspection is a spec-level feature, enabled by default in most implementations, so the complete API surface is usually one query away. And the spec has **no opinion about authentication or authorization** — those are app-level concerns, re-implemented by hand in every resolver. A self-documenting surface plus thousands of individually hand-written access checks is the GraphQL bug profile in one sentence.

## The anatomy that matters on an engagement

A request is parsed once, then executed as a tree of resolver calls — every link below is a place misbehavior lives:

    POST /graphql  {"query": "...", "variables": {...}}
      → parse + validate document against the schema   (errors leak structure)
      → optional protections: depth/complexity limits, WAF rules — often absent
      → executor walks the query tree, top-down:
          Query.user(id)        ← root resolver (hand-written app code)
            └ User.orders       ← child resolver (hand-written app code)
                └ Order.items   ← child resolver (hand-written app code)
      → results stitched into a JSON response shaped like the query

Three structural properties drive most findings:

- **Every field is a separately written function.** A guard on the root query does not cascade down the tree; each of the thirty fields beneath it may or may not re-check who is asking. This is where BOLA/IDOR lives — per-field, inconsistent, forever.
- **The client chooses the response shape.** Nothing binds a type's fields to what the frontend actually renders; every field defined in the schema is a candidate for over-exposure the UI never asked for.
- **Everything funnels through one HTTP endpoint.** Rate limiters, WAFs, and caches that count or filter *requests* see one innocuous POST — while aliases and query batching multiply the actual work inside it, and missing depth limits multiply it again per nesting level.

## Where you'll meet it

- **Mounted next to REST on an existing backend** — `/api/graphql` bolted onto an Express, Django, or FastAPI service. This is usually the thinnest-reviewed code on the host: the middleware and dependency-injection guard stack the REST routes rely on frequently does not wrap resolvers.
- **Dedicated gateways and federated graphs** — an Apollo router splitting queries across internal subgraphs. The router is often the *only* enforcement point (`@authenticated`, scope checks, safelisting); a subgraph reachable directly skips all of it.
- **Mobile and SPA backends-for-frontend** — single-purpose graph APIs in the GitHub/Shopify mold: enormous schemas, deprecated fields from three redesigns ago, long-lived mobile tokens.
- **Developer furniture left in production** — GraphiQL, Playground, or Altair consoles at `/graphiql` or on the endpoint itself: free interactive access to the schema and a query editor.

## Reading the framework's mind

Six facts predict how a GraphQL target behaves on the wire; each expands into a full method in [[graphql-bug-bounty|the methodology]]:

1. **Introspection is on by default** — one query dumps every type, field, and argument. The recon phase is usually a single request.
2. **Disabled introspection leaks anyway** — validation errors, "Did you mean" hints, and wordlist probing reconstruct most of the schema.
3. **AuthN is middleware; authZ is per-resolver** — the login gate works fine while individual resolvers happily serve objects to whoever asks for them by ID.
4. **The schema is a superset of the REST API** — fields that exist in the types but never appear in REST responses are over-exposure candidates.
5. **Aliases and batching pack N operations into one request** — a rate limiter counting requests counts exactly one.
6. **Depth and complexity limits are opt-in** — most servers ship without them; deeply nested, recursive, or fragment-fan-out queries are the DoS lever.

```graphql
query { user(id: 2) { email role apiKey } }
# GraphQL's only promise is shape: the response mirrors the request exactly.
# Whether *you* may read user 2 — or its apiKey — nothing in the stack checked.
```

## Where to go next

[[graphql-bug-bounty|GraphQL bug bounty methodology]] — the phased field guide: endpoint discovery and engine fingerprinting, introspection mining (and schema reconstruction when it is off), the BOLA two-account method, field-level over-exposure diffed against the REST schema, mutation and authorization gaps, batching/aliasing rate-limit bypass, depth-limit DoS, and the tool map.
