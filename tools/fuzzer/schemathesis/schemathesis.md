---
name: "schemathesis"
categories:
  - fuzzer
version: "git (2026-11-07)"
homepage: "https://github.com/schemathesis/schemathesis"
origin: "github"
recipe: "upstream"
packaged-by: []
build-system: python
meta-checked: "2026-11-09"
github-repo: "schemathesis/schemathesis"
github-stars: 3760
github-pushed: "2026-11-09"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 2976
gh-activity-checked: "2026-11-09"
worth: 8.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
license: BSD-2-Clause
pypi: "schemathesis"
github-issues: 29
---

# schemathesis

> Property-based API testing tool: fuzzes REST/GraphQL/gRPC APIs from their schemas to find crashes, auth flaws, and data inconsistencies.

## What it's for

Schemathesis (Dmitry Dygalo, originally Stranger6667, now under the schemathesis org) tests OpenAPI, GraphQL, and gRPC APIs by generating inputs directly from the API's own schema, adapting what it learns from server responses mid-run, and chaining operations into realistic stateful workflows. Built on Hypothesis, it targets the failure classes manual testing misses: 500s on edge-case inputs, responses that violate the documented schema, validation bypasses, integration breakage between responses and client expectations, stateful bugs that only appear when operations interact, and broken object-level authorization. It works as a CLI (`schemathesis run`), a pytest integration, or a declarative `schemathesis.toml` config with auth, phases, and per-operation overrides, and produces HTML/JUnit/JSON/HAR reports. Warning: it sends real requests that create, change, and delete data — point it at dev or staging, never production.

## Typical use cases

### Fuzz an API from its OpenAPI schema

One-command schema-driven test run.

```
schemathesis run https://target.example.com/openapi.json
```

Outcome: fetches the schema, generates valid, edge-case, and malformed inputs for every operation, sends them as real requests, and prints failures grouped by check — server errors, schema violations, missing auth enforcement. Add `--report html` to get an offline report page with a reproduction command for every failure.

### Fuzz a gRPC service

Same workflow for protobuf APIs.

```
schemathesis run grpc://localhost:50051
```

Outcome: uses gRPC server reflection to enumerate services and methods, then fuzzes each method's protobuf fields — requires the `schemathesis[grpc]` extra (e.g. `uvx --from 'schemathesis[grpc]' schemathesis run grpc://localhost:50051`).

### Embed schema tests in pytest

Property-based tests that live with the rest of the suite.

```python
import schemathesis

schema = schemathesis.openapi.from_url("https://target.example.com/openapi.json")

@schema.parametrize()
def test_api(case):
    case.call_and_validate()
```

Outcome: every API operation becomes a Hypothesis-driven pytest test; `case.call_and_validate()` sends the generated request and asserts the response against the schema, and any failing case is automatically shrunk to a minimal reproducing input.

### Test stateful workflows

Catch bugs that only appear when operations interact.

```python
APIWorkflow = schema.as_state_machine()
TestAPI = APIWorkflow.TestCase
```

Outcome: builds a state machine from the schema's operation links (create → read → update → delete style chains) and exposes it as a runnable pytest test case — exercising multi-step workflows including object-level authorization checks that single-operation tests cannot reach.

## CLI version note (read before copying any command)

Schemathesis had a breaking CLI rewrite at **4.0.0 (released 2025-06-10)**, and the current line is **4.33.x (4.33.4, 2026-11-12)**. Two things changed that will silently break copied commands:

- **The command is now `st run`.** Current 4.x docs invoke everything as `st run <LOCATION>` (the short `st` binary). Older 3.x tutorials, blog posts, and even the v4.0 migration guide write the long form `schemathesis run <LOCATION>`. Same tool — but copy *flags* from 4.x docs, because the 3.x→4.x jump renamed most of them.
- **The `--hypothesis-*` flag prefix is gone.** The v4 migration guide maps the old names to new ones: `--hypothesis-max-examples` → **`--max-examples`** (`-n`), `--hypothesis-phases`/`--hypothesis-no-phases` → **`--phases`**, `--hypothesis-seed` → `--generation-seed`, `--hypothesis-derandomize` → `--generation-deterministic`, `--hypothesis-database` → `--generation-database`, `--hypothesis-suppress-health-check` → `--suppress-health-check`. Options like `--hypothesis-deadline`/`--hypothesis-verbosity` were dropped outright. A 3.x `--hypothesis-max-examples=10` on a 4.x binary is an error, not a smaller run — and "no error, full run" is the dangerous failure mode when you *thought* you capped volume. Verify with `st run --help` on the installed binary before any real target.

Everything below is written for the 4.x `st run` CLI.

## Bug bounty workflow with schemathesis

Schemathesis is the first tool to reach for when a bug bounty target hands you an OpenAPI document — it turns that schema into hundreds of property-based negative tests with one command. But it is also the tool most likely to get you *banned*, because **it sends real requests that create, change, and delete data** (the warning in "What it's for" is not decorative). The whole workflow below exists to square those two facts: maximum schema coverage, minimum blast radius.

This page is the tool companion to the technology-vault methodology the tech vault's FastAPI methodology note (*FastAPI Penetration Testing Methodology*). That guide's **Phase 1 — Attack surface mapping from OpenAPI** is where you mine `openapi.json` into a table of `endpoint × auth × object-id`; schemathesis is how you then *exercise* every row of that table automatically. Fetch the spec exactly as that phase does, then feed it here.

### Phase 0 — Authorization and environment (the step that keeps you in the program)

Before the first request, settle three things in writing against the program's rules of engagement:

1. **Is there a designated test/staging environment?** Schemathesis's default run issues POST/PUT/PATCH/DELETE against every documented mutating operation. On production that means spammed records, deleted objects, triggered emails/webhooks, and billing events — a near-certain scope violation. The clean answer is a program-sanctioned staging host. If there is none, you **must** scope the run to non-destructive operations (see Phase 3) and treat full-surface fuzzing as out of bounds.
2. **What volume is permitted?** A property-based run is inherently many requests per endpoint. Fix a per-operation cap and a rate limit up front (Phase 3) so the run is demonstrably low-volume.
3. **How many identities may you use?** The highest-value checks (`ignored_auth`, `object_level_authorization`) need one and then two authenticated users. Confirm multi-account testing is allowed before registering a second account.

### Phase 1 — Acquire the schema

Get the contract the same way the tech vault's FastAPI methodology note Phase 1 does, and keep a local copy so your run is reproducible and offline-auditable:

```bash
curl -s https://api.example.com/openapi.json -o openapi.json
st run openapi.json --help >/dev/null   # smoke: does this binary parse the spec?
```

`LOCATION` can be the live URL or the saved file. Prefer the saved file as your evidence baseline; point `--url` at the live base if the schema's `servers` block is wrong or missing (`st run openapi.json --url https://api.example.com`). GraphQL and gRPC targets work too (`st run https://api.example.com/graphql`, `st run grpc://...`), but the bulk of bounty value is OpenAPI.

### Phase 2 — Authenticate as a real user

Fuzzing the unauthenticated surface finds little; the bugs live *behind* the auth wall, in the code that assumes the caller is legitimate. Attach credentials so every generated request is sent as a real low-privilege user:

```bash
# Bearer token (the common case) — -H is repeatable
st run openapi.json -H "Authorization: Bearer $TOKEN"

# HTTP Basic
st run openapi.json --auth "$USER:$PASS"
```

There is no dedicated bearer flag in `st run`; `--auth` is HTTP Basic only (`USER:PASS`), so tokens go through `-H`/`--header`. Run first as a single authenticated user to shake out crashes and schema violations, then — if the program allows a second account — graduate to the two-user authorization checks in Phase 4.

### Phase 3 — The low-volume calibrated run

This is the run you are actually authorized to send. Every flag here is a volume or blast-radius control:

```bash
st run openapi.json \
  -H "Authorization: Bearer $TOKEN" \
  --include-method GET \          # read-only scope: only GET operations are exercised
  -n 30 \                         # -n/--max-examples: ≤30 generated cases per operation
  -w 1 \                          # -w/--workers: single worker, serial requests
  --rate-limit 5/s \              # hard ceiling of 5 req/s regardless of worker count
  --request-timeout 10 \          # seconds; abandon a hung request instead of piling on
  --max-failures 20 \             # stop early once you have enough to triage
  --report html,vcr --report-dir ./st-evidence
```

- **`--include-method GET`** is the single most important safety flag for an undesignated (production-adjacent) target: it restricts the whole run to read operations, so nothing is created, mutated, or deleted. Widen only on a sanctioned staging host.
- **`-n`/`--max-examples`** caps cases *per operation* during the fuzzing phase (and distinct call *sequences* during the stateful phase). It has no effect on the `examples`/`coverage` phases. Lowering it is your primary volume dial — keep it small (10–30) for bounty work; the default is **100 per operation**, tuned for thoroughness, not politeness.
- **`-w 1`** keeps requests serial. `--workers auto` scales to CPU cores and will hammer the target — never use it on someone else's API.
- **`--rate-limit`** takes `<limit>/<duration>` with `s`/`m`/`h` units (`5/s`, `100/m`). This is the throttle you cite in the report to prove you stayed low-volume.
- To go even gentler, narrow the surface further with `--include-path-regex "/api/v1/.*"`, `--exclude-deprecated`, or `--include-tag`/`--exclude-tag`.

### Phase 4 — Targeted check runs

After the broad read-only pass, run the high-value checks deliberately. Checks are selected with `-c`/`--checks` (comma-separated) and all are on by default; `--exclude-checks` subtracts from `--checks all`.

```bash
# Crash-hunting: only flag 5xx, nothing else — lowest-noise, highest-signal first pass
st run openapi.json -H "Authorization: Bearer $TOKEN" \
  -c not_a_server_error --include-method GET -n 50 -w 1 --rate-limit 5/s

# Authentication enforcement: does any operation accept a missing/invalid token?
st run openapi.json -H "Authorization: Bearer $TOKEN" \
  -c ignored_auth -w 1 --rate-limit 5/s
```

For **object-level authorization (BOLA / OWASP API1)** — the money finding — schemathesis's `object_level_authorization` check needs two users declared as `peers` under `[auth.wfc]` in a `schemathesis.toml`; it then confirms user A cannot read an object user B owns. This is the automated form of the two-account ID swap from the tech vault's FastAPI methodology note Phase 3, and it is only authorized once the program OKs multi-account testing.

### Phase 5 — Shrink to a minimal PoC and reproduce by hand

When a check fails, schemathesis has already done your triage work. Because it is built on **Hypothesis**, the `shrink` phase (on by default; disable with `--no-shrink`) reduces the failing input to a **minimal reproducing case** — the smallest body/parameter set that still triggers the failure. That minimal case, plus the reproduction command schemathesis prints for each failure, *is* your PoC. Confirm it independently before reporting:

```bash
# Re-run from the recorded cassette (--report vcr writes one) to prove determinism
st replay ./st-evidence/<cassette>.yaml --header "Authorization: Bearer $TOKEN"
# Then reduce to a single curl for the report
curl -i https://api.example.com/<minimal-path> -H "Authorization: Bearer $TOKEN" -d '<minimal-body>'
```

A failure you cannot reproduce with a hand-built `curl` is not yet a report — it may be run-order state or a flaky backend. Shrinking makes that manual reproduction trivial; use it.

### How the phases and shrinking work (and why the PoC is nearly free)

Understanding the phase pipeline tells you what each run actually sends and where a finding came from. Schemathesis runs up to four **test phases**, selected with `--phases` (all on by default):

- **`examples`** (formerly `explicit`) — replays the concrete request examples the schema itself declares. Near-zero generated traffic; the gentlest possible pass.
- **`coverage`** — systematic, deterministic probes: missing required fields/headers, boundary values, unsupported methods. This phase is where `missing_required_header`, `unsupported_method`, and `allow_header_conformance` fire.
- **`fuzzing`** (formerly `generate`) — the Hypothesis-driven engine: it generates random schema-shaped (and, in negative mode, schema-violating) inputs up to `-n`/`--max-examples` cases per operation. This is where most crashes surface.
- **`stateful`** — follows the schema's operation links into create→read→update→delete sequences, capping distinct sequences at `-n`. Home of `use_after_free` and `ensure_resource_availability`.

Inside the fuzzing and stateful phases, Hypothesis works in two movements: it **searches** for a failing input, then **shrinks** it — repeatedly simplifying the failing case (smaller numbers, shorter strings, fewer fields) while the failure still reproduces, until it reaches a local minimum. Shrinking stays enabled by default (`shrink` phase); disable only with `--no-shrink`. The shrunk case is the smallest input that still breaks the server, which is exactly the PoC a triager wants — the tool hands you the minimal reproduction for free. Hypothesis also keeps a local example **database** (`--generation-database`, the `reuse` behavior): a failure found today is remembered and replayed first on the next run, so your reproduction is stable across sessions. For a byte-stable PoC, pin randomness with `--generation-seed <N>` (or `--generation-deterministic`).

The `-m`/`--mode` flag decides *what kind* of data the fuzzing phase generates (default `all`):

- **`positive`** — only schema-valid data. Drives `positive_data_acceptance` and finds crashes triggered by *realistic* inputs the developer should have handled.
- **`negative`** — only schema-violating data (wrong types, out-of-range, missing required). Drives `negative_data_rejection` and is the mode most likely to drive an unexpected value into an injection sink or an unhandled branch — i.e. a 5xx crash surface.
- **`all`** — both, and the right default for crash hunting: it maximizes the chance some value reaches a sink validation was supposed to guard.

## Where the money is with schemathesis

| Priority | Finding class | Check / feature that reaches it | Typical severity | Dupe / noise risk |
|---|---|---|---|---|
| 1 | Missing auth enforcement (operation ignores absent/invalid token) | `ignored_auth` (ST401) | High | Low |
| 2 | BOLA — cross-user object read | `object_level_authorization` (ST402, needs 2 `peers`) | High–Critical | Low |
| 3 | Injection sink behind validation (SQL/command error surfaced in a 5xx) | `not_a_server_error` + manual confirmation | High | Low (manual) |
| 4 | Unhandled-input crash / crash surface (500 on fuzzed value — bad enum, null, boundary int) | `not_a_server_error` (ST101/102) | Low–Medium (higher if it leaks a stack trace) | Medium |
| 5 | Response over-exposure / undocumented sensitive fields in body | `response_schema_conformance` | Low–High (High if `password_hash`/`tenant_id`) | Medium |
| 6 | Use-after-free / stateful ordering (deleted resource still readable) | `use_after_free` (ST301), stateful phase | Medium–High | Low |
| 7 | Validation bypass (API accepts data it should reject) | `negative_data_rejection` (ST201) | Low–Medium | **High** |
| 8 | Slow / resource-consuming endpoint | `max_response_time` (ST112) / `--max-response-time` | Medium | Medium (DoS-adjacent — scope it) |
| 9 | Status-code / content-type nonconformance | `status_code_conformance` (ST103), `content_type_conformance` (ST104–106) | Info–Low | **High** (often won't pay) |

The check families, and exactly what each catches:

- **`not_a_server_error` (ST101/102/113/119)** — the headline check. Flags any **5xx**, plus GraphQL `errors` arrays from failed resolvers and gRPC decode failures. **Why a 5xx from a fuzzed input is a finding worth reporting:** a 500 means an input reached code that was never expected to receive it — an unhandled exception. That is a *crash surface*. Behind it sits one of: an injection sink that validation was supposed to guard (a malformed value reaches an unparameterized SQL fragment or a shell and the interpreter errors), an unhandled enum/`oneOf` branch, an integer/precision overflow, or a null/boundary case the handler never guarded. The 500 itself may be Low; what it *points at* (and what its stack trace leaks) is frequently Medium–High. Fuzzing is how you find the one input in a million the developer forgot.
- **`status_code_conformance` (ST103)** — the response status isn't documented for that operation (a `default` response or `2XX` wildcard satisfies it). Mostly inventory/contract hygiene; low bounty value on its own, but an *undocumented* 500 path overlaps with the crash surface above.
- **`content_type_conformance` (ST104–106)** — the `Content-Type` is missing, malformed, or undocumented versus the schema (`application/*` wildcards respected). Usually Info; occasionally unmasks an endpoint returning HTML/errors where JSON was promised.
- **`response_schema_conformance`** — the response **body** violates its declared JSON Schema. This is the automated read-side of excessive-data-exposure hunting (the tech vault's FastAPI methodology note Phase 3.5): extra fields present in the body but absent from the schema are exactly where `password_hash`, `tenant_id`, and internal flags leak.

Two more that pay in bounty specifically: **`ignored_auth` (ST401)** sends each operation one request with no credentials and one with invalid credentials per security scheme, and expects `401`/`403`/redirect — a 200 is broken function-level authentication. **`object_level_authorization` (ST402)** is the automated BOLA probe. Both map straight onto the authorization phase of the tech vault's FastAPI methodology note.

### Complete check reference (all 15, verified against the checks docs)

| Check | ST code(s) | Group | Default | What it catches |
|---|---|---|---|---|
| `max_response_time` | ST112 | Response timing | **off** (opt in) | responses slower than the configured limit |
| `not_a_server_error` | ST101/102/113/119 | Core response | on | 5xx, GraphQL `errors` arrays, gRPC decode failures |
| `status_code_conformance` | ST103 | Core response | on | status code not documented for the operation |
| `content_type_conformance` | ST104–106 | Core response | on | missing / malformed / undocumented media type |
| `response_headers_conformance` | ST107/108 | Core response | on | response header values violating their schema |
| `response_schema_conformance` | ST104,108–118 | Core response | on | response **body** violating its JSON Schema |
| `negative_data_rejection` | ST201 | Input handling | on (`negative`/`all` mode) | API accepts input the schema says is invalid |
| `positive_data_acceptance` | ST202/206 | Input handling | on (`positive`/`all` mode) | API rejects input the schema says is valid |
| `missing_required_header` | ST203 | Input handling | on (coverage phase) | request missing a required header isn't rejected |
| `unsupported_method` | ST204 | Input handling | on (coverage phase) | undefined HTTP method not `405`'d (expects `Allow`) |
| `allow_header_conformance` | ST205 | Input handling | on (coverage phase) | `OPTIONS` `Allow` header disagrees with the schema |
| `use_after_free` | ST301 | Stateful | on (stateful phase) | deleted resource still accessible |
| `ensure_resource_availability` | ST302 | Stateful | on (stateful phase) | created resource not immediately accessible |
| `ignored_auth` | ST401 | Security | on | auth not enforced (missing/invalid creds still `2xx`) |
| `object_level_authorization` | ST402 | Security | on (needs ≥2 `peers`) | one user reads another user's object (BOLA / API1) |

All checks are enabled by default *except* `max_response_time`, which you turn on with `--max-response-time <seconds>` (or `max_response_time` in config) and which is independent of `--checks`/`--exclude-checks`. The stateful checks (`use_after_free`, `ensure_resource_availability`) and `object_level_authorization` only fire in the stateful phase / with the right config, never under a single `case.call_and_validate()`. The ST codes are worth quoting in reports — they pin exactly which property the server violated.

## Command recipes

Annotated command → expected-output blocks. Every one is calibrated low-volume; the throttles are the point.

### Read-only smoke run (safe on an undesignated target)

```bash
st run openapi.json -H "Authorization: Bearer $TOKEN" \
  --include-method GET -n 10 -w 1 --rate-limit 2/s --request-timeout 10
```
Expected: schemathesis lists each GET operation, runs ≤10 generated cases each at ≤2 req/s, and prints a per-check summary. A clean run ends with exit code `0`; any failure is `1`. Nothing is created or deleted — GET-only.

### Crash-surface-only pass (lowest noise)

```bash
st run openapi.json -H "Authorization: Bearer $TOKEN" \
  -c not_a_server_error --include-method GET -n 50 -w 1 --rate-limit 5/s \
  --report json --report-dir ./st-evidence
```
Expected: only 5xx responses are reported; conformance noise is suppressed. Each failure prints the generated input and a reproduction command. `--report json` writes a single machine-readable verdict document under `./st-evidence` for the report appendix.

### Full staging run with evidence capture (sanctioned test host only)

```bash
st run https://staging.example.com/openapi.json -H "Authorization: Bearer $TOKEN" \
  --checks all -n 30 -w 1 --rate-limit 10/s --request-timeout 15 \
  --max-failures 50 --continue-on-failure \
  --report html,vcr,json --report-dir ./st-full
```
Expected: all operations (including mutating ones — *staging only*) exercised across the examples/coverage/fuzzing/stateful phases. `--continue-on-failure` runs every check rather than stopping at the first fault; `--max-failures 50` still bounds the volume. HTML report lands in `./st-full/` as a browsable `index.html`; the VCR cassette lets you `st replay` any failure.

### Phase control to keep volume predictable

```bash
# Replay only the schema's documented examples — near-zero generated traffic
st run openapi.json -H "Authorization: Bearer $TOKEN" --phases examples

# Fuzzing without the stateful phase (no multi-step create/delete chains)
st run openapi.json -H "Authorization: Bearer $TOKEN" --phases examples,coverage,fuzzing --include-method GET
```
`--phases` accepts `examples,coverage,fuzzing,stateful` (all on by default). Dropping `stateful` removes the create→read→update→delete sequences — useful when you want zero chained mutations.

### Scope narrowing

```bash
st run openapi.json --include-path-regex "/api/v1/.*" --exclude-method DELETE \
  --exclude-deprecated --exclude-tag-regex "admin|internal"
```
`--include-/--exclude-{path,method,name,tag,operation-id}` match exactly; the `-regex` variants match patterns. Use these to carve the exact slice the program authorized.

### Pin the run in a config file (`schemathesis.toml`)

Re-typing volume flags every run is error-prone; a config file makes your low-volume, authorized scope identical and version-controllable. Every key below is verified against the configuration reference:

```toml
# schemathesis.toml — low-volume, authorized-scope defaults
workers = 1
rate-limit = "5/s"                                      # units: s, m, h
request-timeout = 10.0                                  # seconds
headers = { Authorization = "Bearer ${API_TOKEN}" }    # token from env, never hard-coded

[generation]
max-examples = 20          # default is 100 per operation; drop it hard for bounty work
no-shrink = false          # keep shrinking on — the minimal case is your PoC

[checks]
negative_data_rejection.enabled = false   # silence the noisiest check (see Pitfalls)
max_response_time = 5.0                    # opt in to the timing check with a 5s ceiling

[[operations]]
exclude-method = "DELETE"  # never let the fuzzer delete on this target
```

```bash
st run openapi.json --config-file ./schemathesis.toml
```

Note the key spellings: `max-examples`, `rate-limit`, `request-timeout` are hyphenated, but `max_response_time` uses an underscore, and `workers`/`rate-limit`/`request-timeout` are top-level (not inside a table). Keeping scope and volume in a committed config — rather than ad-hoc flags — is your audit trail that testing stayed inside the rules of engagement, the same discipline as amass's `scope.txt`. `[auth.wfc]` (the two-`peers` setup the `object_level_authorization` check needs) is referenced by the checks docs but not shown in the configuration reference — confirm its exact syntax with `st run --help`/the checks reference on your installed version before relying on it.

## Pitfalls, noise and scope discipline

**Destructive requests against production is the cardinal sin.** The default run deletes and mutates real data. Hunters get banned for running `st run <prod-url>` with no method scoping and wiping records or flooding a mailbox with triggered notifications. Defenses: `--include-method GET` on anything not a designated test host; a sanctioned staging environment for full-surface runs; and reading the program's rules on automated testing *first*.

**Volume creep.** `--workers auto` and a high `-n` turn a "test" into a stress test. A property-based run with default settings can be thousands of requests. Always pin `-w 1`, a small `-n`, and `--rate-limit`; these three numbers are what you quote to prove you were low-volume. Note that `--rate-limit` throttles the *aggregate* run, so it holds even if you forget to drop workers.

**`negative_data_rejection` is the noisiest check.** It flags any case where the API returned a success status for input the schema says is invalid. Real APIs legitimately coerce or tolerate loose input constantly, so this check produces a flood of "findings" that are design choices, not vulnerabilities. Triage it manually and only report a rejection failure when the accepted-but-invalid input causes real downstream harm (a bypassed business rule, a value that corrupts state). Reporting raw `negative_data_rejection` output is a fast way to lose triager trust.

**Conformance checks rarely pay.** `status_code_conformance` and `content_type_conformance` catch spec drift — valuable for the API owner, usually **informational** for a bounty. Don't file "undocumented 404" as a vulnerability. The exception is when nonconformance *is* the bug: an undocumented 500, or a content-type mismatch that reveals an error-handling path.

**A single 5xx is a lead, not a confirmed finding.** Before reporting, shrink it (Phase 5), reproduce it by hand, and characterize *why* it 500s. "Endpoint returns 500 on `{"age": -1}`" is weak; "negative `age` reaches an unparameterized SQL `LIMIT` and the Postgres error leaks the query in the 500 body" is a report. The 500 is the crash surface; the write-up is the impact behind it.

**Data pollution and cleanup.** Even on staging, a fuzz run litters the database with junk records. Note what you created, clean up test accounts and objects where you can, and never exfiltrate more than the PoC needs — the same discipline as the tech vault's FastAPI methodology note Phase 8 reporting.

**Flaky, order-dependent failures.** Stateful-phase failures can depend on run order or a dirty backend. If `st replay` on the cassette doesn't reproduce it deterministically, it isn't ready to report. Hypothesis shrinking plus a clean replay is your reproducibility bar.

**Version mismatch (see the CLI note).** A 3.x command on a 4.x binary silently ignores renamed flags — you *think* you set `--hypothesis-max-examples=5` and instead run the full default. Confirm flags against `st run --help` on the actual binary.

### What a clean run proves — and what it does not

Schemathesis is a conformance-and-crash engine, not a full authorization or business-logic tester. Be honest in the report about the boundary, the same way the tech vault's FastAPI methodology note's coverage-closeout section frames claims you *tried to disprove*:

- **It proves:** under the inputs it generated (bounded by `-n` and `--mode`), no operation returned a 5xx, every response matched its declared status/content-type/schema, and — where enabled — missing/invalid credentials were rejected. A clean `not_a_server_error` run is evidence the crash surface held *for the inputs tried*; it is never a proof of absence (property-based testing samples, it does not exhaust).
- **It does not prove authorization is correct** beyond the two automated checks. `ignored_auth` catches "no credential works"; `object_level_authorization` (with peers) catches one BOLA shape. It says nothing about BFLA, tenant isolation on complex objects, scope enforcement, or privilege escalation via mass assignment — those remain the manual two-account work of the tech vault's FastAPI methodology note Phase 3.
- **It does not prove sensitive data stays server-side.** `response_schema_conformance` flags *undocumented* fields, but a schema that legitimately documents `tenant_id`/`email` passes the check while still over-exposing. Confirming what actually ships is a manual read, exactly the honest gap the the tech vault's FastAPI methodology note tooling appendix names.
- **It does not understand your business logic.** Coupon reuse, negative transfers, workflow-order abuse, and rate-limit bypasses are invisible to schema-derived generation. Use schemathesis to clear the mechanical surface fast, then spend the saved time on the logic bugs it cannot see.

Frame residual risk accordingly: "no server errors or schema violations across N generated cases per operation" is a defensible, bounded claim — not "the API is secure."

## Reporting evidence

The minimal reproducing case Hypothesis shrank to is the centerpiece of the report. Capture, for each finding:

1. **The failing check and its code.** Name the check (`not_a_server_error`, `ignored_auth`, …) and the `ST###` failure code schemathesis prints — it tells the triager exactly what property was violated.
2. **The shrunk minimal input.** The smallest body/parameters that still trigger the failure, copied from schemathesis's output. This is what distinguishes a real report from "the fuzzer found something."
3. **A hand-built `curl` reproduction.** One request, expected vs. actual response, so the triager reproduces it without installing schemathesis. Derive it from the minimal case.
4. **The request/response pair.** The full request schemathesis sent and the server's response — especially the 5xx body, where stack traces, SQL errors, and file paths leak. Redact sensitive data you did not need to prove the bug.
5. **The run's provenance.** The exact `st run` command (with `-n`, `-w`, `--rate-limit` visible) to document that testing was authorized-scope and low-volume, plus the schemathesis version (`st --version`).
6. **The reports as an appendix, not the body.** `--report html` produces a browsable `index.html` (under `--report-dir`); `--report json` a single verdict document; `--report vcr` a replayable cassette; `--report junit` for CI evidence. Attach these as supporting artifacts, but lead the report with the minimal hand-reproduced PoC — a triager should not need to open an HTML bundle to understand the bug.

Severity follows the finding class, not the tool: a `not_a_server_error` that leaks a SQL error is High; the same 500 with an opaque body is Low–Medium; `ignored_auth` and `object_level_authorization` failures are High–Critical. Map to the program's rubric and cite the OWASP API Security Top 10 category (API1 for BOLA, API2 for `ignored_auth`) the triager expects.

## References

- Schemathesis CLI reference (`st run`, flags, exit codes) — https://schemathesis.readthedocs.io/en/stable/reference/cli/
- Schemathesis checks reference (all 15 built-in checks, ST codes, default states) — https://schemathesis.readthedocs.io/en/stable/reference/checks/
- Schemathesis configuration reference (`schemathesis.toml`, `[auth.wfc]` peers) — https://schemathesis.github.io/schemathesis/reference/configuration
- Schemathesis v4.0.0 release notes (breaking CLI changes; released 2025-06-10) — https://github.com/schemathesis/schemathesis/releases/tag/v4.0.0
- Schemathesis v3→v4 migration guide (flag renames, `--phases`, `--no-shrink`) — https://raw.githubusercontent.com/schemathesis/schemathesis/v4.0.0/MIGRATION.md
- Schemathesis releases (latest: 4.33.4, 2026-11-12) — https://github.com/schemathesis/schemathesis/releases
- Schemathesis repository — https://github.com/schemathesis/schemathesis
- Companion methodology: the tech vault's FastAPI methodology note — FastAPI Penetration Testing Methodology (Phase 1 OpenAPI mapping, Phase 3 BOLA)
- OWASP API Security Top 10 (2023) — https://owasp.org/www-project-api-security/

*Research supplement added 2026-11-15, grounded in the Schemathesis official docs (CLI, checks, and configuration references at schemathesis.readthedocs.io / schemathesis.github.io), the v4.0.0 release notes and v3→v4 MIGRATION.md, and the GitHub release history (latest 4.33.4, 2026-11-12); companion methodology the tech vault's FastAPI methodology note (model: claude-opus-4-8).*
