---
technology: Next.js
kind: overview
audience: offensive-security
family: web-platforms
layer: "Full-stack React meta-framework (App Router / React Server Components, middleware edge layer, self-hosted Node server)"
language: JavaScript/TypeScript (Node.js runtime)
first-released: 2016
homepage: "https://nextjs.org"
github-repo: "https://github.com/vercel/next.js"
evidence: "3 writeups (strict command-context gate over merged text+ocr corpus: npm install next@/package.json next pins, next.config.*, .next/ artifacts, Next.js fingerprint headers, x-middleware-subrequest / CVE-2025-29927, CVE-2025-55182 / React2Shell, Next-Action server-action traffic; one parent-seeded machine collapsed at the gate to a bare X-Powered-By: Next.js fingerprint line with no Next.js chain — excluded, and the sweep's other hits (a 'next' JSON pagination key, an unrelated Next-Action string) were adjudicated out)"
status: thin
provenance: "corpus-mined from 3 strict-gated writeups in the writeup corpus (see ../internal coverage ledger) + upstream-verified: Vercel blog CVE-2025-29927 + GHSA-f82v-jwr5-mffw (middleware authorization bypass via x-middleware-subrequest; fixed 12.3.5/13.5.9/14.2.25/15.2.3; self-hosted next start + output:standalone affected, Vercel/Netlify/static-export not), react.dev RSC advisory + nextjs.org CVE-2025-66478 + vendor analyses of CVE-2025-55182 'React2Shell' (Flight-protocol unsafe deserialization; React 19.0.0/19.1.0/19.1.1/19.2.0 react-server-dom-*; Next.js App Router 14.3.0-canary.77+/15.x/16.0.0-16.0.6; fixed 15.0.5/15.1.9/15.2.6/15.3.6/15.4.8/15.5.7/16.0.7; Pages Router and 13.x/14.x stable not affected). Accessed 2026-11-12"
---

# Next.js

> Next.js fuses client and server into one process and then draws the security boundary in the softest place possible — a middleware layer that can be skipped with one header, and a deserializer that runs on every POST because the framework assumes its own client built the payload.

## What it is

Next.js (Vercel, 2016) is the React meta-framework that owns the whole server side of an application: routing (the App Router renders React Server Components server-side, shipping serialized component trees over the "Flight" protocol), API routes and Server Functions (server-side code callable straight from React components), and an edge middleware layer that runs before requests reach any route — which applications habitually load with authentication and authorization checks. Self-hosted deployments run `next start` against a `.next/` build output, or ship `output: 'standalone'` bundles; static exports (`next export`-lineage) render everything ahead of time and execute no server code.

The operator's mental model: **one Node process holding the whole application — presentation, API, auth gate, and server runtime — reachable through framework-generated endpoints you did not have to discover**. The two framework-level bugs that define the target are both about misplaced trust in that design: the middleware trusts an internal header it can receive from outsiders (CVE-2025-29927), and the Server Component runtime trusts the structure of serialized payloads because it assumed only Next.js's own client would construct them (CVE-2025-55182, "React2Shell").

The takeaway: fingerprinting Next.js is free (the `X-Powered-By: Next.js` header is sent unless suppressed, `__next` static paths and build IDs leak), and the version pin is usually recoverable from `package.json` or the build output — at which point the engagement reduces to checking the two CVE classes below against the deployment shape, then falling back to the Server Actions/`middleware.ts` source review that the build artifacts hand you.

## The anatomy that matters on an engagement

    browser → [optional edge proxy] → next start (Node, .next/ build)
               → middleware.ts (auth cookie checks — SKIPPABLE via x-middleware-subrequest)
               → route handlers / pages (App Router: RSC render, Flight serialization)
               → Server Functions (POST + Next-Action header → server-side execution)
               → backing services (DBs, auth providers, the 127.0.0.1 surface incl. node --inspect :9229)

    attack A: CVE-2025-29927 — forge the internal recursion header → middleware never runs
             → reach routes whose authz lived ONLY in middleware
    attack B: CVE-2025-55182 — crafted Flight payload to a Server Function endpoint
             → unsafe deserialization → prototype pollution → RCE as the app user
    attack C: mine the .next/ build artifacts (routes-manifest, compiled server chunks)
             → hardcoded secrets, internal routes, source-level logic
    attack D: replay Server Actions headlessly (Next-Action id + $ACTION_* fields)
             → drive server-side logic without the UI
    attack E: post-foothold → Node inspector on 9229 (started with --inspect) → code exec as app user
             (the debugger hunt itself is the [[node-express]] unit's)

Three structural properties drive most findings:

1. **Middleware is a gate that proves nothing.** `middleware.ts` intercepts requests before routes, so teams put cookie/token validation there — and the middleware result is trusted downstream. The internal `x-middleware-subrequest` header (recursion guard) lets an external request mark the middleware as already-run, skipping it entirely; authorization that lived only in middleware disappears with it.
2. **The Flight protocol deserializes what the POST body says.** Server Functions accept a serialized argument structure; vulnerable React Server Components runtimes reconstruct server-side objects from attacker-controlled JSON without validating the shape, so a single crafted POST becomes prototype pollution and then arbitrary code execution — no authentication, because the deserializer sits below the application's own auth.
3. **The build directory is a source-code leak by design.** `.next/` holds the compiled server (route manifests, server chunks, middleware code) — readable after any file-read or path-traversal foothold, and routinely containing credentials that only "exist" server-side.

## Where you'll meet it

- **Self-hosted single-process deployments** (`next start`, `output: 'standalone'` containers) — the exposed shape for both CVE classes; the process often carries the whole internal surface on loopback (databases, caches, the V8 inspector on 9229 when started with `--inspect`).
- **Vercel/Netlify-hosted fronts** — platform edge strips or neutralizes the middleware-bypass header class; the application's own authz bugs and information leaks remain, the framework-header bypass largely does not.
- **Static exports** — no server runtime, no middleware, no Server Functions: the Next.js-specific classes are gone and only the generated static content matters.
- **Developer and staging boxes** — version pins visible in `package.json`, `next.config.js` in the repo root, `.next/` from a local build, and the Node process started with inspector flags.

## Reading the framework's mind

- **Fingerprint to version, then check two lists.** `X-Powered-By: Next.js` (suppressed only by config), `/__next` static paths and the build ID, `package.json`'s `"next"` pin, `next.config.js` in the root. CVE-2025-29927 (middleware bypass) affects `>= 11.1.4 < 12.3.5`, `13.x < 13.5.9`, `14.x < 14.2.25`, `15.x < 15.2.3` — a 15.2.2 pin, for instance, is vulnerable. CVE-2025-55182/"React2Shell" (with Next.js's merged CVE-2025-66478) affects App Router builds from `14.3.0-canary.77` and all 15.x up to per-line fixes, plus `16.0.0`–`16.0.6` — verified fixed lines are 15.0.5 / 15.1.9 / 15.2.6 / 15.3.6 / 15.4.8 / 15.5.7 / 16.0.7; the vulnerable React packages are `react-server-dom-webpack`/`-parcel`/`-turbopack` at 19.0.0, 19.1.0, 19.1.1, 19.2.0 (fixed 19.0.1 / 19.1.2 / 19.2.1). Pages Router applications, 13.x, and 14.x stable are **not** affected by React2Shell.
- **The middleware bypass is a one-header test.** Send `X-Middleware-Subrequest: <middleware-name>` (the value is the middleware's name — a colon-separated chain; public PoCs repeat the default name `middleware`). If a route that middleware was guarding now answers, authorization lived only in middleware. Patch-side, deployments that cannot upgrade strip the header at their reverse proxy — so a target behind a cleaning proxy needs the test aimed past it (direct origin, alternate vhost).
- **React2Shell needs App Router + React 19 and nothing else** — no credentials, no user interaction; the POST targets any Server Function endpoint with a crafted Flight payload (public exploit tooling exists under the name React2Shell). Exploitation drove real-world mass compromise after disclosure, landing it on CISA's KEV list; expect patched internet-facing assets but unpatched internals.
- **Server Actions are replayable without the browser.** Pages using Server Actions embed hidden `$ACTION_ID`/`$ACTION_*` fields and a `Next-Action` header carrying the action id; parse them out of the rendered HTML and the action becomes a plain scriptable POST endpoint — the reliable way to drive server-side logic (and any injection it contains) at wordlist speed.
- **`.next/` turns file reads into source review.** `routes-manifest.json` maps every route (including ones no UI links to); compiled server chunks contain the middleware and route logic in readable form; secrets hardcoded for "server-only" code land in the bundle. Grab the directory whole whenever traversal allows.
- **After a foothold, check the process command line for `--inspect`.** A Next.js server started with the Node inspector bound to 127.0.0.1:9229 is a direct path from app-user code execution to a full debugging session — that hunt (inspector protocol, payload technique) is owned by the [[node-express]] unit; the Next.js-specific move is simply remembering the loopback surface exists inside the same process.

## Where to go next

[[next-js-bug-bounty|Next.js bug bounty methodology]] — phased: fingerprint + version pin → middleware-bypass test (CVE-2025-29927) → RSC deserialization (CVE-2025-55182/React2Shell) → Server Action replay → `.next/` artifact mining → loopback/inspector escalation.
