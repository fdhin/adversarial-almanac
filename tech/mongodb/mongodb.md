---
technology: "MongoDB"
kind: overview
audience: offensive-security
family: databases
layer: data store
language: C/C++
homepage: https://www.mongodb.com
github-repo: https://github.com/mongodb/mongo
provenance: "corpus-grounded (11 writeups, strict marker gate) + upstream-verified (MongoDB docs/security advisories)"
---

# MongoDB

> A document database whose two canonical deployment sins — **access control off** and **query operators exposed to user input** — account for nearly every real-world compromise: walk-in `mongosh` sessions and `$ne` login bypasses.

## What it is

MongoDB stores JSON-shaped documents (BSON on the wire) in collections instead of tables, queried through JavaScript-flavored operators (`find`, `$gt`, `$ne`, `$regex`, `$where`) over port **27017** via the `mongod` daemon — `mongos` fronts sharded clusters. It burst into web stacks with Node.js (mongoose remains the dominant driver pattern), which is exactly why it shows up compromised: tutorial-grade deployments ship `mongoose.connect('mongodb://localhost/app')` with no users configured, and the framework's query flexibility means an HTTP parameter can become part of the query object itself.

The operator's core insight: **the query language is the injection surface**. There is no string-concatenation SQL grammar to escape — instead, applications that pass user-supplied keys or objects into query builders let the attacker supply *operators*, and a login check becomes `find({user: "admin", pass: {$ne: "x"}})`, which matches every admin row. The second insight is default posture: since MongoDB 3.6 `mongod` binds to localhost only, but authentication is never on until someone runs with `--auth` and creates users — and every legacy tutorial, container image default override (`--bind_ip_all`), and "quick fix" `net.bindIp: 0.0.0.0` re-opens the front door.

## The anatomy that matters on an engagement

```
browser ──HTTP──► app (Node/Express, Python, Java)
                    │  builds query object from request (★ injection: $ne/$regex/$where)
                    ▼
               mongoose / driver ──TCP 27017──► mongod (★ no-auth walk-in; localhost-or-0.0.0.0 bind)
                                                  ├─ admin & app databases (creds, sessions, tokens)
                                                  └─ files on disk (dbPath) ← readable post-shell
```

Structural properties that drive findings:

1. **Creds live in app config, not in mongo** — `.env` (`URL_DATABASE="mongodb://..."`), `connect_db.js`, `dbURI` constants. Find the config, get the URI (often credential-free).
2. **Operators are the grammar** — `$ne`/`$gt`/`$nin`/`$regex` bypass auth checks; `$regex` + response oracle = blind row-by-row extraction; `$where` runs JavaScript.
3. **No auth by default** — an exposed 27017 usually means full read/write of every database with `mongosh`, no password ever prompted.

## Where you'll meet it

- **Node/Express web apps** — login forms and search endpoints passing objects; the NoSQLi pattern (form-encoded `password[$ne]=` or JSON `{"password":{"$ne":null}}`).
- **Backend API stacks** (Strapi, Mattermost-style admin panels) — reset/bypass endpoints whose parameters reach Mongo operators (`{"code":{"$gt":0}}`).
- **Docker/Kubernetes internal networks** — mongod bound inside a bridge network (`172.17.0.x:27017`), reachable after any container foothold; tunnel with chisel or socat and it is a walk-in.
- **Bare-metal legacy** — pre-3.6 or mis-bound instances on 0.0.0.0; the classic unauthenticated 27017 exposed to the internet (Meow/Enigmapass-era ransomware wave).

## Reading the daemon's mind

- Binds localhost by default since 3.6; `--bind_ip_all` / `net.bindIpAll` / `0.0.0.0` in configs = someone widened it (grep `mongod.conf`).
- `mongod` without `--auth`/`security.authorization: enabled` has **no users checked** — `mongosh` connects silently.
- Default auth (when enabled) is SCRAM over the `admin` database; URI `mongodb://user:pass@host/db?authSource=admin`.
- `show dbs` from a walk-in lists everything; app DBs hide creds/hashes in `users` collections.
- Wire protocol is binary BSON on 27017 — nmap's mongodb-* NSE scripts fingerprint version and enumerate databases without any client.

## Where to go next

- [[mongodb-bug-bounty]] — full method: port recon → walk-in enum → NoSQL operator injection (form and JSON) → blind `$regex` extraction → URI hunting → tunneling to containerized instances.
