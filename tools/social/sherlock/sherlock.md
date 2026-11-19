---
name: "sherlock"
categories:
  - blackarch-social
version: "0.16.0"
homepage: "https://github.com/sherlock-project/sherlock"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-01-13"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "sherlock-project/sherlock"
github-stars: 99671
github-pushed: "2026-11-07"
github-archived: false
github-issues: 362
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 57
gh-activity-checked: "2026-11-09"
---

# sherlock

> Hunts a username across 400+ social networks in one pass and reports every site where the account exists.

## What it's for

Sherlock (originally by Siddharth Dushantha, now maintained by the Sherlock Project) answers the OSINT question "where does this username already exist?" — given one or more handles it probes hundreds of social-network profile URLs in parallel and reports hits with direct links. Use it to pivot from a single known alias to a full footprint, to check candidate usernames before registering them, or to feed a wider investigation with profile URLs. It checks exact username matches only (with a `{?}` wildcard for `-`/`_`/`.` variants) and does not attempt passwords, private data or breached credentials.

## Typical use cases

### Check a single username everywhere

```
sherlock user
```

Outcome: progress output per site, then a summary of found profiles with their URLs; a `user.txt` file with the hits is written to the working directory.

### Check several usernames and keep results grouped

```
sherlock --folderoutput results user1 user2 user3
```

Outcome: one `<username>.txt` per handle under `results/`, so multi-alias investigations stay separated. Add `--csv` or `--xlsx` to also emit a spreadsheet of every site checked and its verdict.

### Limit the scan to specific sites or site types

```
sherlock --site github --site reddit user
```

Outcome: only the named sites are probed instead of the full 400+ list — faster when you already know which platforms matter to the case.

### Route checks through a proxy

```
sherlock --proxy socks5://127.0.0.1:1080 --timeout 30 user
```

Outcome: all probes go out through the proxy with a per-request timeout (default 60s), keeping your source address out of target-site logs and tolerating slow circuits.

## Bug bounty workflow with sherlock

Sherlock is a *passive* username-footprinting tool: given a handle it fires one HTTP probe per site at 400+ third-party platforms and reports where the account exists. It never touches credentials, never requests private data, and — this is the scope subtlety that trips people up — **it does not send traffic to your target at all.** The requests go to GitHub, Reddit, Instagram, and hundreds of other sites, not to `app.example.com`. That makes it low-risk against the in-scope asset but puts you in a relationship with third parties whose terms of service you are still bound by. Treat it exactly like maigret: enumeration only, authorized identifiers only, and every "hit" is a *lead* until you have manually opened the profile.

The value for a bounty program is rarely "this username exists" (that is Info and duped to death). It is the *chain*: a reused org/service handle whose *account* is **unregistered** on a platform where impersonation or squatting has teeth, a developer's personal account that links back to a repo leaking a scoped secret, or a recycled handle that re-enables account takeover. Sherlock finds the breadcrumbs; you build the finding.

### Phase 0 — Authorization and scope gate

Before the first run, confirm three things against the program's rules of engagement:

- **OSINT / username enumeration is in scope.** Many web-app programs scope *assets* (domains, apps) and are silent on OSINT. Enumerating an org's *service* handles (`acme-ci`, `acme-support`) to prove a registerable-namespace risk is usually defensible; enumerating a *named employee's personal* accounts is usually not, and de-anonymizing a pseudonymous individual is almost never in scope. When in doubt, restrict seeds to org-owned service identifiers and ask.
- **The output feeds a scoped asset.** "This handle exists on 30 sites" is not a finding. "This org's npm account is unregistered, so an attacker can register it and publish under the org's trusted name" is. Keep the line of sight from the sherlock hit to an in-scope impact.
- **No credentials, no private content.** Sherlock only reads public profile existence. Keep it that way — do not log into any discovered account, and do not scrape profile contents beyond the single screenshot needed to prove the account is real.

### Phase 1 — Seed handles and a reproducible baseline

Start from one authorized seed handle and pin the data so the run is defensible later:

```
sherlock --local --timeout 60 --csv --print-all acme-ci
```

`--local` forces the bundled `sherlock_project/resources/data.json` instead of the live remote manifest at `https://data.sherlockproject.xyz`, so your run is reproducible and you can state exactly which site definitions you used. `--csv` writes `acme-ci.csv` with one row per site (`username,name,url_main,url_user,exists,http_status,response_time_s`) — the machine-readable record for your report. `--print-all` keeps the "not found" rows so you can distinguish *confirmed absent* (a takeover candidate) from *not checked*. Record `sherlock --version` and the run date alongside it.

For an investigation spanning several org handles, keep them grouped:

```
sherlock --folderoutput results --csv --local acme-ci acme-support acme-dev
```

With `--folderoutput` the per-handle CSVs land under `results/` (add `--txt` if you also want the plain URL lists grouped there), so multi-alias cases stay separated. Note the hard rule: `--output` (single file) and `--folderoutput` cannot be combined, and `--output` requires exactly one username.

### Phase 2 — Triage CLAIMED vs AVAILABLE vs WAF/UNKNOWN

Sherlock assigns every probe one of five internal statuses — `CLAIMED`, `AVAILABLE`, `UNKNOWN`, `ILLEGAL`, `WAF` — and the triage you do on them is the whole game:

- **CLAIMED** — the account exists. A *lead* for correlation (Phase 3), not yet a fact: open it.
- **AVAILABLE** — the username was not found, i.e. potentially registerable. This is your **takeover/impersonation surface** (Phase 4) when the same handle is claimed on the org's canonical platforms.
- **WAF** — a Cloudflare / AWS CloudFront / PerimeterX fingerprint was seen; the result is unreliable in *both* directions (false positive and false negative). Never report a WAF row without manual confirmation.
- **UNKNOWN** — the site errored or timed out; re-run that site with a longer `--timeout` before concluding anything.
- **ILLEGAL** — the username failed the site's `regexCheck`, so no request was sent (e.g. a handle too long or with disallowed characters for that platform). Expected, not a finding.

The output caveat that bites hunters: **by default sherlock writes no file at all.** You only get persisted results with `--txt`, `--csv`, or `--xlsx`. Build the habit of always passing one.

### Phase 3 — Pivot and correlate (the account-linkage payout)

Every CLAIMED hit is a node. The finding is usually an *edge*: a link from an org identity to something scoped. Working order:

1. **Open each CLAIMED profile manually** and confirm it is the subject (matching avatar, bio linking back to the org, cross-referenced email). A matching handle alone is not proof — unrelated people share usernames.
2. **Follow code-hosting and paste platforms first.** A developer's GitHub/GitLab/Bitbucket profile reached from an org handle is the highest-yield pivot: public repos, gists, and commit emails routinely leak secrets, internal hostnames, and `.env` fragments that *are* in scope as the real finding (the sherlock hit is just how you found them).
3. **Expand the seed with the `{?}` wildcard** to catch separator variants the org actually uses:

```
sherlock --local --csv "acme{?}ci"
```

`{?}` expands to three handles — `acme_ci`, `acme-ci`, `acme.ci` — in one invocation, covering the common service-account naming drift.

4. **Note recycled or abandoned accounts.** An account that exists but is clearly dormant (no activity, a deleted-then-recreated profile) can be an *orphaned SaaS account* still holding privilege, or a handle a platform will let someone else re-register — both are takeover angles for Phase 4.

### Phase 4 — Takeover-surface validation (without taking anything over)

For each AVAILABLE result on a platform where the org's handle is claimed elsewhere, confirm the registerability *passively*:

- Load the profile URL from the CSV (`url_user`) in a browser and confirm the platform returns its real "user not found" / "sign up" page — not a soft 404 or a WAF challenge that fooled sherlock.
- Prioritise platforms where an unclaimed *account* has teeth: package and container registries (`npm`, `PyPi`, `RubyGems`, `Docker Hub` — squatting the org account to publish/post under its trusted name), developer platforms, and anything that drives trust by handle (support/brand impersonation).
- **Do not register the handle, publish a package, or create the account.** Proving the namespace is *free* is the finding; claiming it is acting on the vulnerability and usually a rules violation. If the program wants proof of registerability, the live "available"/"sign up" page is it.

### Phase 5 — Package for a correlation report

Convert the spreadsheet into a narrative that separates *tool output* from *verified fact* from *impact* — see Reporting evidence below. The sherlock CSV is cited as method; the finding is the verified URLs and the chain between them.

### How a verdict is decided (and why the site list's quality matters)

Sherlock's accuracy is only as good as its per-site definitions in `data.json`, and understanding the detection logic is what lets you predict which verdicts to distrust. For each site the manifest carries a small schema — `urlMain`, `url` (the profile template with a `{}` placeholder for the handle), an optional `urlProbe` (a different URL hit for detection than the one shown to you), optional `headers` and `request_payload`, a `request_method` (`GET`/`HEAD`/`POST`/`PUT`), an optional `regexCheck`, and an `errorType` with its `errorMsg`/`errorCode`. The `errorType` is the whole ballgame:

- **`status_code`** — existence is inferred purely from the HTTP status. Sherlock sends a `HEAD` request here (cheap, no body). Brittle: any platform that returns `200` for both real and missing profiles, or that soft-404s, flips the verdict.
- **`message`** — sherlock fetches the page (`GET`) and looks for a known "not found" string (`errorMsg`). Breaks silently the moment the site rewords that string or changes its markup — the kind of drift that makes a stale manifest report false positives.
- **`response_url`** — existence is inferred from whether the request redirected; sherlock disables redirects for these sites and compares the landing URL. A changed redirect target poisons the result.

Two more gates run before any request: a `regexCheck` that rejects handles a platform could never accept (yielding the `ILLEGAL` status, *not* a network call), and the exclusions list — by default sherlock fetches `false_positive_exclusions.txt` from the repo's `exclusions` branch (10-second timeout) and drops sites with known-bad definitions. `--ignore-exclusions` keeps them (more coverage, more false positives); `--local` also skips exclusions because it loads the bundled definitions directly. Every probe goes out with a *fixed* `User-Agent` (a Firefox 129 Linux string), which is itself a fingerprint some WAFs key on — another reason WAF rows and `message`-type sites deserve manual confirmation.

The timeliness takeaway: the live manifest at `https://data.sherlockproject.xyz` is updated precisely so "false positives which have already been fixed" don't resurface, so a *fresh* run is more accurate — but a *pinned* run (`--local` or `--json <file>`) is more *defensible*. For reporting, pin and record the version; for a last accuracy pass before you submit, re-check the specific sites you rely on against the live manifest. To vet a proposed fix before it ships, `--json <PR-number>` loads the manifest at that pull request's head.

## Where the value is with sherlock

| Finding class | How sherlock reaches it | Typical severity | Dupe / scope risk |
|---|---|---|---|
| Unclaimed *account* for an org handle enabling impersonation / squatting | `AVAILABLE` on a dev/registry/brand platform where the org handle is canonical elsewhere | Medium–High | Low dupe if tied to a concrete abuse path; often needs OSINT explicitly in scope |
| Account-linkage pivot to a scoped secret (dev handle → repo/gist leak) | `CLAIMED` on GitHub/GitLab/paste sites → manual repo review | High (the *leak* is the finding) | Low — the secret is the payout; the username is just the trail |
| Recycled / re-registerable handle enabling account takeover | `AVAILABLE` on a platform that lets a freed username be re-bound to an existing identity | High | Medium — depends on the platform's re-binding behavior |
| Orphaned / abandoned SaaS or service account | `CLAIMED` but dormant account tied to the org | Medium–High | Medium — impact hinges on residual privilege |
| Brand / support-handle impersonation surface | `AVAILABLE` on high-trust social platforms | Low–Medium | High dupe; low severity alone |
| Identity correlation / de-anonymization of a person | Full `CLAIMED` footprint across platforms | Info–Low | Usually **out of scope**; handle with care |

The rule of thumb mirrors maigret's: **existence is Info; an unclaimed account plus a concrete abuse path, or a claimed account that chains to an in-scope secret, is the finding.** Lead with the chain, not the list.

**What sherlock actually proves (read this before writing "dependency confusion").** Every check is a *user-profile existence* probe, not a package-name or scope claimability check. On the registries that matter for takeover work the manifest targets the account page — `npm` → `https://www.npmjs.com/~{}`, `PyPi` → `https://pypi.org/user/{}`, `RubyGems` → `https://rubygems.org/profiles/{}`, `Docker Hub` → `https://hub.docker.com/u/{}/`. An `AVAILABLE` there means the org's *account/handle* is unregistered, which is an **account-squatting / impersonation** foothold (register the account, publish or post under the org's trusted name). It is **not** proof that a specific internal *package name* is publishable — classic dependency confusion targets package names and needs a separate check you do by hand. Report what you proved: "the org's npm account `acme-ci` is unregistered," not "dependency confusion exists."

### sherlock vs maigret: picking the right username tool

They answer the same question and are easy to confuse; pick deliberately. Sherlock is the *fast, narrow* instrument — a flat 400+-site CLAIMED/AVAILABLE sweep with a hard 20-worker cap, no recursion, no tags, no HTML/PDF/graph output. Reach for it to get a quick, reproducible existence map (`--local --csv`) and to surface AVAILABLE namespaces for takeover work. [[maigret|maigret]] is the *deep, correlational* instrument on a far larger site list: `--tags` to scope by category (`coding`, `tech`), recursive search that extracts new identifiers (emails, names) from found profiles and re-queries them, `--json ndjson` plus HTML/PDF/`--graph`/`--neo4j` reports, and a `--self-check` that tests its own detectors before a run. Reach for maigret when the *correlation graph* is the deliverable or when you need category-scoped, recursive pivoting.

A common two-tool flow: sherlock first for a fast CLAIMED/AVAILABLE pass and the registerable-namespace list, then maigret on the confirmed handles when you need the linked-identity graph for the report. Note the anonymization asymmetry — maigret still exposes Tor-related plumbing, whereas sherlock has dropped `--tor`/`--unique-tor` entirely and routes only through `--proxy`.

## Command recipes

All examples use the authorized placeholder handle `acme-ci`. Sherlock has **no rate-limit or delay flag** and caps concurrency at 20 workers internally (you cannot raise or lower that from the CLI), so your only levers on third-party request volume are *fewer sites* (`--site`) and *one handle at a time*.

```
# Reproducible single-handle baseline, machine-readable, keep the "not found" rows
sherlock --local --timeout 60 --csv --print-all acme-ci
# -> acme-ci.csv: username,name,url_main,url_user,exists,http_status,response_time_s
#    "exists" column = Claimed / Available / Unknown — your triage input
```

```
# Narrow to the platforms that matter to the case — lowest-volume, fastest, kindest to third parties
sherlock --site github --site gitlab --site npm --local --csv acme-ci
# -> only the named sites are probed (case-insensitive match); unknown names abort with
#    "Error: Desired sites not found: ..."
```

```
# Route every probe through a proxy to keep your source IP out of third-party logs
sherlock --proxy socks5://127.0.0.1:1080 --timeout 90 --local --csv acme-ci
# -> prints "Using the proxy: socks5://127.0.0.1:1080"; raise --timeout because proxied
#    circuits are slower and a short timeout manufactures UNKNOWN/false-negative rows
```

```
# Separator variants in one run (service-account naming drift)
sherlock --local --csv "acme{?}ci"
# -> checks acme_ci, acme-ci, acme.ci
```

```
# Group a multi-alias investigation; add a spreadsheet of every site + verdict
sherlock --folderoutput results --csv --xlsx --local acme-ci acme-support acme-dev
# -> results/ holds per-handle text; <handle>.csv / <handle>.xlsx written per username
#    (note: --xlsx ignores --folderoutput and writes <handle>.xlsx to the CWD)
```

```
# Test a proposed site addition before it ships, or pin an exact manifest state
sherlock --json 2600 acme-ci            # load the data.json from upstream PR #2600
sherlock --json ./pinned-data.json acme-ci   # or a local/remote JSON you control
# -> --json accepts a file path, a URL, or a bare PR number (fetched at that PR's head)
```

```
# Deliberately re-introduce suppressed false positives (coverage/QA pass, not reporting)
sherlock --local --ignore-exclusions --print-all acme-ci
# -> upstream maintains an exclusions list of known-bad site results; --ignore-exclusions
#    turns it off "(may return more false positives)". --local also bypasses exclusions.
```

```
# Debug a single suspicious verdict
sherlock --site somesite --dump-response --verbose acme-ci
# -> dumps the raw HTTP response so you can see why a site was Claimed/Available/WAF
```

```
# Open every hit in the browser to speed up the manual-verification step (Phase 3)
sherlock --browse --local --csv acme-ci
# -> after the run, each found profile is opened in your default browser; pair with
#    --csv so you keep the record while you eyeball the live pages
```

```
# Only pull NSFW sites in when the case actually needs them (off by default)
sherlock --nsfw --local --csv acme-ci
# -> NSFW site definitions are excluded unless --nsfw is passed; keep them out of a
#    routine corporate footprint report unless the scope calls for them. --site can
#    still select a specific NSFW platform even without --nsfw.
```

Low-volume discipline in one line: **`--local` + `--site <the few that matter>` + one handle** is the kindest, most reproducible run; reach for the full 400-site sweep only when the case genuinely needs breadth, and never loop it over a handle list in a tight shell loop.

```
# Correlation over time: diff today's run against last week's to catch NEW accounts
sherlock --local --csv --print-all acme-ci   # writes acme-ci.csv
# ... later, after mv acme-ci.csv acme-ci.prev.csv and re-running:
# compare the CLAIMED rows between the two CSVs by url_user to see what appeared
#   diff <(awk -F, '$5=="Claimed"{print $4}' acme-ci.prev.csv | sort) \
#        <(awk -F, '$5=="Claimed"{print $4}' acme-ci.csv | sort)
# -> a handle that flipped Available->Claimed is a freshly registered account to
#    investigate (legit expansion, or an impersonator squatting the org's name)
```

Because `--csv` is stable and column-addressable (`exists` is column 5, `url_user` column 4), a pinned (`--local`) run makes a clean monitoring baseline: the row-level diff between two dated CSVs is your "what changed" report, and a column of `url_user` values across several seed handles pivots straight into a cross-handle correlation matrix.

### Timeout, proxy, and request-method mechanics

Four behaviors shape how reliable and how quiet a run is:

- **`--timeout` (default 60s, must be positive)** is applied per request. Too *low* manufactures `UNKNOWN`/false-negative rows on slow platforms — a timed-out probe is not a "not found". Too *high* lets a few stuck sites stretch the run. For a baseline, 60s is fine; raise it when proxying or when `UNKNOWN` rows cluster, and re-run just those sites rather than the whole list.
- **Request method is per-site, not global.** `status_code`-type sites get a lightweight `HEAD`; others get a `GET`; a handful declare `POST`/`PUT` in the manifest. `response_url`-type sites run with redirects disabled. You cannot change this from the CLI — it is a property of each site definition — which is why `--dump-response` is the right tool when a single verdict looks wrong.
- **`--proxy socks5://host:port`** routes both HTTP and HTTPS through one proxy and prints `Using the proxy: ...` at startup. It keeps your source IP out of the third-party logs and, pointed at a Tor SOCKS port, is the only way to get Tor behavior now that the dedicated flags are gone. Expect proxied circuits to be slower — raise `--timeout` to match, or you trade anonymity for a wave of false `UNKNOWN`s.
- **Startup touches GitHub.** On launch sherlock queries the GitHub releases API (10s timeout) to check for a newer version and prints an "Update available!" line if so — a harmless but non-silent network call to be aware of in a locked-down testing environment.

## Pitfalls, noise and scope discipline

- **Every hit is a lead, not a fact.** Sherlock's verdict is a heuristic over each site's `errorType` (`message`, `status_code`, or `response_url`). Sites change their markup, their status codes, and their anti-bot posture constantly, so both false positives (a soft 404 that returns 200) and false negatives happen. **Open every CLAIMED profile and confirm every AVAILABLE "not found" page manually before it enters a report.**
- **WAF rows are untrustworthy in both directions.** A `WAF` status (Cloudflare / AWS CloudFront / PerimeterX fingerprint) means the real answer is hidden — the source itself warns WAFs "will occasionally block Sherlock and lead to false positives and negatives." Re-check those platforms by hand or via a proxy; never report a WAF-derived verdict.
- **The site list is live and drifts.** By default sherlock pulls the manifest from `https://data.sherlockproject.xyz` at runtime, so two runs days apart can check different sites and give different verdicts. For anything you will report, pin the manifest with `--local` (bundled definitions) or `--json <file>` (a definition set you archived), and record which you used. Reproducibility is what lets a triager re-run your evidence.
- **Default writes nothing to disk.** Current sherlock persists results only with `--txt`, `--csv`, or `--xlsx`. If you forget, your "evidence" is scrollback. Always pass an output flag.
- **No throttle, 20 parallel workers, straight at third parties.** There is no rate-limit, delay, or thread-count flag. A full sweep is a burst of ~400 requests across 20 connections to hundreds of unrelated sites — which can trip *their* abuse protections and, run in a loop over many handles, looks like scanning. Narrow with `--site`, run one handle at a time, and proxy if attribution matters. Remember the volume lands on third parties who did not sign your program's RoE.
- **Tor is gone; proxy is the only anonymization path.** Older sherlock shipped `--tor` / `--unique-tor`; the current tool does not. Use `--proxy socks5://...` (point it at your own Tor SOCKS port if you want Tor) — do not cite flags that no longer exist.
- **Scope creep into people.** The single biggest way to get an OSINT report rejected (or your account actioned) is enumerating a *named individual's* personal accounts, or de-anonymizing a pseudonym, when the program scoped org assets. Keep seeds to authorized org/service handles unless the RoE explicitly permits person-level OSINT.
- **Acting on the takeover surface.** Registering an AVAILABLE namespace, publishing a dependency-confusion package, or creating the impersonating account crosses from *finding* to *exploitation*. The free "sign up / user not found" page is your proof; claiming the handle is a rules violation.
- **Don't confuse AVAILABLE with safe-to-ignore.** `AVAILABLE` is the interesting column for takeover work, but only when the handle is *canonically the org's* elsewhere. A random unclaimed handle is noise; an unclaimed `acme-official` where `acme-official` is the verified brand handle on three other platforms is the report.
- **Profile existence ≠ package-name claimability.** Sherlock's registry checks (`npm`, `PyPi`, `RubyGems`, `Docker Hub`) probe the *account/profile* page, not a package or scope name. An `AVAILABLE` verdict proves the org *account* is unregistered (account-squatting / impersonation), not that a particular internal package name can be published. Don't inflate it into a "dependency confusion" claim without the separate package-name check — a triager will catch the gap.

## Reporting evidence

Separate *tool output* from *verified fact* from *impact*. Capture:

- **The exact command and environment:** the full invocation with flags, `sherlock --version`, the run date, and whether the manifest was the live remote, `--local`, or a pinned `--json` file. This is what makes the run reproducible — state it explicitly.
- **Machine-readable results:** the `--csv` (or `--xlsx`) file as the authoritative record of every site checked and its verdict (`exists` = Claimed/Available/Unknown, plus `http_status` and `response_time_s`). It is greppable, diffable between runs, and free of terminal color noise. Run with `--print-all` so "confirmed absent" rows are present, not just hits.
- **Per-account proof:** for every CLAIMED account you rely on, the exact profile URL (`url_user` from the CSV), a screenshot of the live page, and the corroborating identifier (shared avatar, bio link, matching email) that ties it to the subject — not merely the matching handle. State which accounts you opened and verified versus which are raw tool leads.
- **Takeover-surface proof:** for every AVAILABLE namespace you report, a screenshot of the platform's live "user not found" / "sign up" page proving the handle is free, plus the canonical platforms where the org *does* own the handle (the contrast is the impact). Never a registration.
- **The chain, spelled out:** how the sherlock breadcrumb reaches a scoped impact — e.g. unregistered npm account → squatting/impersonation publish path; or CLAIMED dev GitHub → public gist → live-looking secret (report the secret per RoE as its own finding, after confirming validity is permitted).
- **Scope and privacy statement:** the authorization under which each handle was enumerated, a note that no credentials were tested and no private content accessed, and confirmation that out-of-scope PII was minimized or discarded. In an OSINT report this section carries as much weight as the findings.

### Finding write-up skeleton

```
Title: Org CI handle "acme-ci" npm account is unregistered — impersonation / account-squatting risk

Summary: "acme-ci" is CLAIMED on GitHub, GitLab and Docker Hub (the org's canonical
  service identity) but the npm *account* (npmjs.com/~acme-ci) is AVAILABLE, letting an
  attacker register it and publish/post under the org's trusted name.

Scope & authorization: Enumeration of org service handles is authorized under RoE §<x>.
  No credentials were tested; no private content was accessed. Out-of-scope PII discarded.

Method:
  sherlock --local --csv --print-all acme-ci
  sherlock --version -> v0.16.0 ; manifest: --local (bundled) ; run date: 2026-11-15

Verified (opened manually):
  - https://github.com/acme-ci        [CLAIMED]    avatar + org link confirm ownership
  - https://gitlab.com/acme-ci        [CLAIMED]    confirmed
  - https://www.npmjs.com/~acme-ci    [AVAILABLE]  live "not found" page attached

Impact & chain:
  1. npm account free -> attacker registers acme-ci on npm, publishes/posts under the
     org's trusted identity; developers who trust the handle are misled. (Whether a
     specific internal package name is also claimable is a separate, hand-verified step.)

Remediation: register/park the org handle on npm (and other registries); enforce
  scoped-package naming; add account-squatting / dependency-confusion monitoring.

Evidence attached: acme-ci.csv, screenshots of each verified URL (incl. the npm 404).
```

The account-linkage variant, where the sherlock hit is purely the trail to an in-scope leak:

```
Title: Developer handle reuse exposes a live credential in a public gist (via OSINT pivot)

Summary: The org's deploy handle "acme-deploy" (CLAIMED on GitHub) links to a public
  gist containing what appears to be a live API key for the in-scope API.

Scope & authorization: OSINT pivot from an authorized org handle (RoE §<x>). The key's
  validity was [confirmed per RoE / NOT tested — pick one]; no private data accessed.

Method:
  sherlock --local --site github --csv acme-deploy
  sherlock --version -> v0.16.0 ; manifest: --local ; run date: 2026-11-15

Pivot chain (each step opened manually):
  - https://github.com/acme-deploy           [CLAIMED]  avatar + org link confirm owner
  -> public gist https://gist.github.com/acme-deploy/<id>  contains APIKEY=<redacted>
  -> key format matches the in-scope API's documented scheme

Impact: the leaked key, if live, grants <scoped access> to the in-scope API.

Remediation: rotate the key immediately; add secret scanning to CI; separate personal
  and service identities; enforce MFA on the account.

Evidence attached: acme-deploy.csv, screenshots of the profile and the (redacted) gist.
```

The sherlock run is one line of the Method block; the finding is the gist URL and the key. Keep that proportion — the tool located the breadcrumb, you verified the leak.

Everything attacker-facing in these skeletons is a *verified* URL or an *absence you confirmed* — the sherlock output is cited as method, never as the finding itself. Frame impact honestly: "these profiles exist" is Info; "this org account is unregistered, here is the free account page and the canonical handles it impersonates" is the finding.

### Coverage closeout — falsifiable claims

Know when you are done and be able to defend it. Frame each claim as something you *tried to disprove*, and record the boundary you did not cross so any residual-risk note stays honest.

| Claim you tried to prove | Rejected (nothing to report) if |
|---|---|
| An org handle is registerable on a platform where impersonation has teeth | every `AVAILABLE` row either failed manual re-check (soft-404/WAF) or lands on a platform with no abuse path |
| A CLAIMED account chains to an in-scope secret or asset | every opened profile is either unrelated, empty, or leaks nothing scoped |
| A reported account actually belongs to the subject | you confirmed ownership (avatar/bio-link/email), not just a matching handle |
| The verdict is real, not a WAF/stale-manifest artifact | you re-checked `WAF`/`message`-type rows by hand against a fresh manifest |
| Enumeration stayed within scope | only authorized org/service handles were seeded; no person-level OSINT beyond the RoE |

The claim you can rarely close from a username sweep alone: that an `AVAILABLE` handle is *truly* re-registerable in a way the target can be made to trust — that depends on each platform's re-binding policy, which you confirm from the platform's own behavior, not from sherlock. Name it as a platform property, not a tool output.

## References

- Sherlock Project repository — https://github.com/sherlock-project/sherlock
- Sherlock CLI source (`sherlock_project/sherlock.py`, argument definitions, worker cap, WAF/status handling) — https://raw.githubusercontent.com/sherlock-project/sherlock/master/sherlock_project/sherlock.py
- Sherlock site-manifest handling (`sherlock_project/sites.py`: `MANIFEST_URL`, `EXCLUSIONS_URL`, NSFW/exclusion logic) — https://raw.githubusercontent.com/sherlock-project/sherlock/master/sherlock_project/sites.py
- Sherlock package metadata / update-check URL (`sherlock_project/__init__.py`) — https://raw.githubusercontent.com/sherlock-project/sherlock/master/sherlock_project/__init__.py
- Sherlock installation guide — https://sherlockproject.xyz/installation
- Sherlock project homepage ("400+ social networks") — https://sherlockproject.xyz/
- Companion scope framing — [[maigret]]

*Research supplement added 2026-11-15, grounded in the sherlock-project/sherlock source (`sherlock.py`, `sites.py`, `result.py`, `__init__.py`, master branch) and the sherlockproject.xyz installation/homepage (model: claude-opus-4-8). Re-verified 2026-11-16 against the same source: every flag cited exists, the 20-worker cap and absence of any rate/thread flag hold, and the CSV `exists` column renders `QueryStatus` names (`Claimed`/`Available`/`Unknown`/`Illegal`/`WAF`), not booleans — the monitoring diff was corrected to filter `$5=="Claimed"` accordingly. Note: the current tool has no `--tor`/`--unique-tor` and no rate/thread flag (concurrency is capped at 20 workers internally); anonymization is via `--proxy` only, and no result file is written unless `--txt`/`--csv`/`--xlsx` is passed.*
