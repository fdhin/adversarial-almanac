---
name: "maigret"
categories:
  - blackarch-social
version: "0.6.5"
homepage: "https://github.com/soxoj/maigret"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-10-24"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "soxoj/maigret"
github-stars: 39785
github-pushed: "2026-11-07"
github-archived: false
github-issues: 51
github-match: "exact"
gh-checked: "2026-11-07"
worth: 9.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 626
gh-activity-checked: "2026-11-09"
---

# maigret

> Collect a dossier on a person by checking a username across thousands of sites.

## What it's for

Maigret checks one or more usernames against a maintained database of thousands of sites in parallel, reports every site where the name is claimed, and extracts extra identifiers (IDs, linked usernames) found on matching profiles — then packages the results as terminal output plus reports in HTML, PDF, XMind, CSV, NDJSON, an interactive graph, or a Neo4j import script. It supports tag-based site filtering (e.g. only photo or dating sites), keyword highlighting inside found pages, recursive search seeded from one profile URL, username permutation from a real name, Tor/I2P/proxy routing, an optional AI-generated investigation summary, a built-in web UI, and use as an async Python library. It finds accounts, not passwords; a minority of sites need an experimental Cloudflare bypass (local FlareSolverr).

## Typical use cases

### Run a full username check

```
maigret user
```

Outcome: parallel checks across the site database, with a terminal report of claimed/unclaimed sites per username and report files written for the run.

### Narrow the search with site tags

```
maigret user --tags photo,dating
```

Outcome: only sites tagged `photo` or `dating` are queried — a faster, quieter pass when the question is specifically photo or dating profiles.

### Generate engagement-ready reports

```
maigret user --html --pdf
```

Outcome: writes an HTML report and a PDF dossier of all findings in addition to the terminal summary; `--csv`, `--txt`, `--json ndjson`, `--graph`, and `--neo4j` give machine-readable or graph exports.

### Expand one real name into variants

```
maigret "john doe" --permute
```

Outcome: generates likely username variants (`johndoe`, `j.doe`, ...) from the inputs and searches all of them; `--parse https://target.example.com/~user` instead seeds a recursive search from an existing profile page.

### Route checks through Tor

```
maigret user --tor-proxy socks5://127.0.0.1:9050
```

Outcome: all site checks egress through the Tor SOCKS gateway (a plain `--proxy` or `--i2p-proxy` works the same), avoiding datacenter-IP blocks; start the daemon yourself first.

### Serve the web interface

```
maigret --web 5000
```

Outcome: starts a local web UI on http://127.0.0.1:5000 where queries can be launched and results browsed with a graph view and downloadable reports.

## Bug bounty workflow with maigret

**Scope first, always.** Maigret is an OSINT username-enumeration engine, not a web-application scanner. It produces no exploit and touches no target asset — it queries thousands of *third-party* sites for a handle. That inverts the usual bounty risk model: the danger is not breaking the target, it is (1) collecting personal data outside the program's rules, (2) hammering unrelated third parties from your IP, and (3) reporting false positives. Every phase below assumes the program's rules of engagement explicitly permit OSINT/identity-correlation work on the identifiers you feed it. Account-takeover and credential-exposure programs usually scope *the organization's own assets and test identities*, not arbitrary people. If you cannot point to the line in the policy that authorizes enumerating a given username, do not run it.

This tool finds *where accounts exist*. It never finds or tests passwords. Any pivot from maigret output toward credential stuffing, password spraying, or unauthorized login is out of scope on every legitimate program — the defensible deliverable is a *credential-exposure / account-hardening* report, never a login attempt.

### Phase 0 — Authorization, identifiers, and a baseline

Goal: establish exactly which identifiers are in scope and confirm the database is current before you generate any findings.

```bash
maigret --version          # pin the version + dependency set into your notes
maigret --self-check       # verify the site DB against live claimed/unclaimed pairs
```

`--self-check` runs each site's known-present and known-absent reference usernames and reports sites whose detection logic has drifted (site redesigns silently turn true results into false positives). Run it at the start of an engagement so you know which sites to distrust; add `--diagnose` for per-site recommendations and `--auto-disable` only if you want it to drop failing sites from the run. Do this *before* enumerating a real target so the baseline reflects the DB, not the subject.

Record in your engagement notes: the maigret version, the date, whether you ran with the top-500 default or `-a`, and the exact identifiers authorized. Those four facts are what make a later report reproducible.

### Phase 1 — Selective, low-noise first pass

Goal: a quiet, fast pass that answers "does this handle exist in the obvious places" before you widen to the full database.

```bash
# Default run already limits to the top 500 sites by Majestic Million rank
maigret targethandle --timeout 30 -n 25 --print-errors
```

Keep the first pass to the default top-500 set (do **not** add `-a` yet) and drop `--max-connections` (`-n`) from its default of 100 to something like 25. The default fans out 100 concurrent connections; that is fine for the tool's one-request-per-site model but makes your egress IP look like a scanner to CDNs and can trigger datacenter-IP blocks that poison the rest of your run. `--print-errors` surfaces captcha/connection/country-ban failures so you can tell "not found" apart from "could not check".

Use `--tags` to answer a *specific* question instead of searching everything, and `--top-sites N` to dial breadth between the 500 default and the full `-a` database:

```bash
maigret targethandle --tags coding        # dev platforms — the ones that leak secrets
maigret targethandle --tags us            # two-letter country code, not a language
maigret targethandle --top-sites 1000     # widen past 500 without going all-in on -a
```

For org work, pre-trim the categories you have no authorization to collect — enumerating an employee's dating or adult profiles is a privacy liability even when the handle genuinely matches:

```bash
maigret targethandle --exclude-tags dating,porn   # blacklist by tag; combines with --tags
```

### Phase 2 — Full enumeration and recursion (when justified)

Goal: widen coverage only after the selective pass shows the handle is live and the program permits broad collection.

```bash
maigret targethandle -a -n 25 --timeout 30 --retries 1 --print-errors
```

`-a`/`--all-sites` searches the full database (thousands of sites) instead of the top 500 — more coverage, far more third-party traffic, and more false positives. Reserve it for handles that matter. Recursion and account-page extraction are **on by default**: maigret reads matched profile pages, pulls out linked usernames/IDs, and seeds new searches automatically. That is powerful for correlation but is also where scope creep begins — a recursive hop can drag in a *different person's* handle discovered on the subject's profile. Disable it when you need tight, defensible scope:

```bash
maigret targethandle -a --no-recursion     # only the handle you authorized, no auto-pivots
```

### Phase 3 — Correlation and pivoting

Goal: turn a set of hits into an identity map, and pivot on the *extra identifiers* maigret extracts (not just the handle).

- **Seed from a known profile.** `--parse <URL>` extracts owner info from an account page or online document, then searches by what it finds — the cleanest way to expand from one confirmed profile to the rest of the footprint.
- **ID-type pivots.** Beyond usernames, maigret searches by platform IDs via `--id-type` (`gaia_id`, `steam_id`, `vk_id`, `yandex_public_id`, `ok_id`, `orcid`, `qq_id`, `bilibili_id`, and others). When extraction surfaces one of these on a profile, re-run with the matching `--id-type` to find linked accounts a username search misses.
- **Name-to-handle expansion.** `--permute` generates handle variants from two or more inputs (a real name, an email local-part) and searches all of them. Use it only when the program authorizes correlating a named individual.
- **Batch from a file.** `--input-file ids.txt` reads one identifier per line; each line may carry an `id_type:value` prefix so one run mixes usernames and typed IDs. `--permute` applies only to positional usernames, not to file input.

### Phase 4 — Validate every finding before it enters a report

Goal: no reported account that you have not visited yourself.

Maigret is a triage engine, not an oracle. Its own docs warn that tag markup is unstable and that site detection drifts — a "found" line is a lead, not proof. The censorship/captcha detector flags a run when more than ~3% of checks return the same error type, which is your cue that a whole batch of results may be unreliable. For each account you intend to report:

1. Open the exact URL maigret printed and confirm the profile is real and belongs to the subject (match avatar, bio, linked IDs — not just a colliding handle).
2. Cross-check against the `--print-not-found` output so you can state what was explicitly *absent*, not merely unchecked.
3. Re-run suspect sites after a fresh `--self-check` if the DB may have drifted.

Handle collisions are the dominant false-positive source: a common username "found" on 40 sites is usually 40 different people. Correlate on extracted identifiers (same email, same linked profile, same avatar hash), never on the string alone.

**Reading the terminal output.** `[+]` is a claimed/found profile, `[-]` is not found (only shown with `--print-not-found`), and `[++]` in bright green marks a page that matched a `--keywords` term — scan for those first when hunting in-scope leaks. The run ends with a found-count summary; with `--print-errors` you also see per-site captcha/connection/country-ban failures, which is how you separate a true `[-]` from a site that could not be checked. Treat a high error rate (the >~3% censorship/captcha warning) as a signal to re-run, not as clean "not found" data.

**Recovering deleted or changed profiles.** Maigret's archive/mirror checking (e.g. Picuki for Instagram) can surface a cached copy of a profile that has since been taken down — useful when a subject scrubbed an account between your passes. A mirror hit is still a lead: confirm it against the archive's own URL and note that it is a cached/mirrored copy, not the live profile, in the report.

### Phase 5 — Package the deliverable

Generate the machine-readable and human-readable artifacts together, then write the narrative yourself (see Reporting evidence):

```bash
maigret targethandle -a --json ndjson -H -fo ./engagement-results -n 25
```

### Run-selection matrix

Pick the narrowest run that answers the question in front of you — breadth is third-party traffic and false positives, not free coverage.

| Situation | Run | Why this and not `-a` |
|---|---|---|
| First look at a handle | default (top 500), `-n 25` | Quiet, fast, enough to confirm the handle is live |
| "Does this dev leak secrets?" | `--tags coding --keywords <org,host,secret-prefix>` | Targets the platforms that leak and flags matches as `[++]` |
| Correlate a confirmed profile | `--parse <URL>` | Pivots on extracted IDs, not guesswork |
| Named individual (authorized) | `--permute` from name/email parts | Expands variants without a broad web sweep |
| Tight scope, no third parties | `--no-recursion` (+ `--exclude-tags`) | Stops auto-pivots into other people's handles |
| Full footprint, justified | `-a --no-recursion -n 25` | Max coverage with scope creep disabled |
| Reproducible evidence run | `--no-autoupdate --db <pinned>` | Same DB state as a prior run |

---

## Where the value is with maigret

Maigret findings are rarely standalone bounties — most username-enumeration results are Info/Low in a VDP. The value comes from *chaining*: maigret cheaply locates the account that another technique turns into impact. Rank by that downstream chain, not by the raw hit count.

| Priority | Finding class | Feature / command that reaches it | Typical severity | Dupe / false-positive risk |
|---|---|---|---|---|
| 1 | Exposed developer accounts (GitHub/GitLab/Docker Hub/npm) that leak secrets or private infra | `--tags coding` then manual review of each dev profile | Medium–High (via leaked secret / takeover) | Low value dupe; must verify secret exposure yourself |
| 2 | Account-takeover surface: one reused handle across many auth surfaces of the *target org* | `-a` + recursion, correlate on extracted email/IDs | Medium–High when it chains to ATO | High false-positive from handle collisions |
| 3 | Credential-exposure correlation for a hardening report (handle reuse → recommend MFA/breach-password checks) | NDJSON export + graph, cross-referenced with breach data you already hold | Low–Medium (defensive value) | Report framing only — never test the creds |
| 4 | Org/employee attack-surface mapping (shadow accounts, forgotten service handles) | `--input-file` of authorized handles, `--tags` by platform | Low–Info unless it exposes an asset | Medium — scope creep into non-employees |
| 5 | Dangling/registerable service accounts tied to the org's handle | `-a`, then check which platforms show the handle *unclaimed* (`--print-not-found`) | Low–Medium (impersonation / takeover of the namespace) | Low once verified on the live site |
| 6 | Leaked PII / linked identifiers surfaced on matched profiles | Default extraction + `--parse` | Info (and a liability — see scope discipline) | High — easy to over-collect |

The honest summary: maigret's best bounty role is **recon that feeds a higher-severity bug** — it tells you the developer whose GitHub to read, the namespace the org left registerable, the handle whose reuse you then argue as account-takeover risk. Reported as "I found these profiles," it is a dupe or an Info; reported as "this reused handle plus this exposed secret equals account takeover of a scoped asset," it is a finding.

---

## Command recipes

Each block is annotated with the low-volume setting that keeps you off third-party blocklists. Maigret's load on any *single* site is light (roughly one request per site per username), so the risk is aggregate egress volume and false positives, not per-site DoS — tune for quietness and accuracy, not for throttling one host.

**Quiet selective pass (start here):**
```bash
maigret targethandle --timeout 30 -n 25 --print-errors
# Default top-500 sites, 25 concurrent connections instead of 100, errors shown.
# Expect: per-site [+]/[-] lines, a found-count summary, report files under ./results.
```

**Dev-platform sweep (highest-value tag for bounty work):**
```bash
maigret targethandle --tags coding -n 25 --print-not-found
# Narrows to coding/dev sites; --print-not-found lets you state registerable namespaces.
# Expect: a short list of dev platforms with claimed/unclaimed status per site.
```

**Keyword highlighting (surface secret/infra leaks on matched profiles):**
```bash
maigret targethandle --tags coding --keywords acme-corp "internal.example.com" aws_
# Pages containing any keyword are marked [++] in bright green instead of plain [+].
# Point the keywords at employer names, internal hostnames, or secret prefixes so a
# profile that mentions them jumps out — the fast path from "account exists" to
# "account leaks something in scope."
```

**Vanity-domain leads (experimental, treat as leads only):**
```bash
maigret targethandle --with-domains
# DNS A-record checks of {username}.<tld> patterns. Results are leads, not confirmations
# — a resolving domain is a lead to verify, never a reported finding on its own.
```

**Full enumeration, scope-tight (no auto-pivot to other identities):**
```bash
maigret targethandle -a --no-recursion -n 25 --timeout 30 --retries 1 --print-errors
# -a = full DB; --no-recursion stops maigret from searching handles it scrapes off profiles.
# Expect: thousands of checks; more hits and more collisions — verify each.
```

**Seed a correlation from one confirmed profile:**
```bash
maigret --parse https://example-platform.example.com/u/targethandle
# Extracts owner info + linked IDs from that page, then searches by what it finds.
# Expect: discovered identifiers echoed, followed by searches seeded from them.
```

**Batch of authorized org handles from a file:**
```bash
printf 'acme-dev\nsteam_id:76500000000000000\n' > ids.txt
maigret --input-file ids.txt -n 25 --print-errors
# One identifier per line; optional id_type: prefix mixes usernames and typed IDs.
# '#' and blank lines are skipped. --permute does NOT apply to file input.
```

**ID-type pivot (when extraction surfaced a platform ID):**
```bash
maigret 76500000000000000 --id-type steam_id
# Searches by a non-username identifier; types include gaia_id, vk_id, orcid, qq_id, etc.
```

**Name-to-handle expansion (named-subject correlation, if authorized):**
```bash
maigret john doe --permute -n 25
# Builds variants (johndoe, j.doe, ...) from the inputs and searches them all.
```

**Routing (block-evasion and .onion/.i2p coverage):**
```bash
maigret targethandle --proxy socks5://127.0.0.1:1080      # all checks via one HTTP/SOCKS proxy
maigret targethandle --tor-proxy socks5://127.0.0.1:9050  # ONLY .onion sites; others skipped without it
maigret targethandle --i2p-proxy http://127.0.0.1:4444    # ONLY .i2p sites
# Note: --tor-proxy/--i2p-proxy are scheme-scoped to their networks; use --proxy to route everything.
```

**Database verification and maintenance (not a scan of the subject):**
```bash
maigret --self-check --diagnose          # report drifted sites, with recommendations
maigret --self-check --auto-disable      # also disable sites that fail their reference check
maigret --submit https://new-site.example.com/users/ref   # propose a new site for the DB
```

**Interfaces and automation (triage UI, offline DB, pipeline integration):**
```bash
maigret --web 5000                 # local web UI at http://127.0.0.1:5000 for interactive triage
maigret targethandle --db ./sites.json            # load a custom/offline site DB (path or URL)
maigret targethandle --no-autoupdate --db ./sites.json   # pin the DB for a reproducible run
```
For pipeline work, maigret ships as an async Python package (`import maigret`) — the upstream Telegram bot is the reference embedding. Driving it as a library lets you feed authorized identifiers from a recon system, apply the same `-n`/tag discipline programmatically, and consume results as structured objects instead of parsing terminal output. Keep the same scope controls you would use on the CLI: cap concurrency, restrict sites by tag, and disable recursion when the authorization is for a single identifier.

**Report bundle for the deliverable:**
```bash
maigret targethandle -a --json ndjson -H -P -fo ./engagement-results -n 25
# NDJSON (machine-readable, one object per line), HTML + PDF dossiers, into a named folder.
# Other formats: -C csv, -T txt, -M md, -X xmind (XMind 8 only), --graph, --neo4j (Cypher).
```

The AI summary is optional and sends data off-box:
```bash
maigret targethandle --ai --ai-model gpt-4o-mini
# Requires an OpenAI-compatible API key (openai_api_key in settings). This ships the
# subject's collected identifiers to a third-party LLM — a privacy/scope decision, not a
# default. Omit it on any engagement where the subject's data must not leave your control.
```

---

## Pitfalls, noise and scope discipline

**Privacy and legality are the real risk surface.** Maigret is dual-use and trivially crosses into stalking, harassment, or unlawful profiling. On a bounty program, only enumerate identifiers the rules of engagement name as in-scope — typically the organization's own assets, service accounts, or explicitly provided test identities. Enumerating a named employee, a customer, or a bystander "because the handle appeared" is out of scope and, in GDPR/CCPA jurisdictions, potentially unlawful processing of personal data. Minimize: prefer `--tags` and the top-500 default over `-a`, use `--no-recursion` to stop auto-pivots into third parties, and delete collected PII that is not part of the finding when the engagement ends.

**Never cross the credential line.** Maigret's output ("account exists here") is one step from temptation. Credential stuffing, password spraying, reset-flow abuse, or any login attempt using the handles you found is out of scope on every legitimate program and likely illegal. The deliverable is a *defensive* credential-exposure report: "this handle is reused across N auth surfaces; recommend MFA and breach-password screening." You document exposure; you do not exercise it.

**False positives are the default failure mode.** Handle collisions make a common username appear "found" everywhere — those are usually different people. Site detection also drifts as sites redesign, which is exactly what `--self-check` catches; the built-in censorship/captcha detector warning (fired when >~3% of checks share one error type) means a batch is unreliable. The tool's own docs call tag markup unstable and extracted/`--with-domains` results *leads, not confirmations*. Rule: nothing enters a report that you have not opened and confirmed belongs to the subject by a corroborating identifier, not the string alone.

**Getting your IP blocked (and poisoning the run).** The default `-n 100` fans out 100 concurrent connections. No single site is stressed, but your egress IP making thousands of requests across the web in seconds looks like scanning and attracts datacenter-IP blocks and captchas — which then show up as false "not found" results. Lower `-n`, raise `--timeout` on flaky networks, and use `--proxy` if your IP is already flagged. `--print-errors` tells you when blocks are corrupting the data.

**The Cloudflare bypass is a ToS and stability landmine.** `--cloudflare-bypass` is experimental and routes protected sites through a local solver (FlareSolverr). It defeats a site's anti-automation control — confirm that doing so is within your authorization and the third-party site's terms before enabling it. Leave it off by default.

**AI summaries leak the subject off-box.** `--ai` sends collected identifiers to an OpenAI-compatible endpoint. On a privacy-sensitive engagement that is an unacceptable disclosure; keep it off unless the subject's data is explicitly allowed to leave your environment.

**Database drift makes old runs unreproducible.** Maigret auto-updates its site DB at startup (`--no-autoupdate` to skip, `--force-update` to force). Two runs days apart can differ purely because the DB changed. Pin the version and note the DB state (or run `--no-autoupdate` against a known DB) when reproducibility matters for a report.

**Disabled sites are disabled for a reason.** `--use-disabled-sites` re-enables sites the maintainers turned off; the docs warn these are a known false-positive source. Do not include them in a run whose results you intend to report, and never on a broad `-a` sweep where you already have more collisions than you can verify. If a specific disabled site matters, re-enable it in isolation and verify every hit by hand.

**Trim the search, not just the output.** When some identifiers in a batch are out of scope or already covered, `--ignore-ids` skips them at search time rather than filtering afterward — fewer third-party requests, less collected data to discard later. Prefer narrowing the input (`--ignore-ids`, `--exclude-tags`, `--top-sites`) over running everything and deleting the overage.

---

## Reporting evidence

For an OSINT/credential-exposure finding the burden is *provenance and accuracy*, not an exploit trace. Capture:

- **The exact command and environment:** full invocation (flags included), `maigret --version` output, the run date, and whether the DB was auto-updated or pinned. This is what makes the run reproducible.
- **Machine-readable results:** the `--json ndjson` file (one object per site, with URL and status) as the authoritative record. It is greppable, diffable between runs, and free of the terminal's color noise.
- **Human-readable dossier:** the `-H`/`-P` HTML/PDF report, and `--graph` or `--neo4j` when the finding is about *correlation* — a graph showing one identity linked across platforms argues reuse far better than a list.
- **Per-account proof:** for every reported account, the exact profile URL, a screenshot of the live page, and the corroborating identifier (shared email, linked ID, matching avatar) that ties it to the subject — not just the matching handle. State explicitly which accounts you manually verified versus which are tool-reported leads.
- **What was absent:** `--print-not-found` output, so the report can distinguish "confirmed not present" from "not checked" — this is what turns an unclaimed org namespace into a reportable impersonation/takeover risk.
- **Scope and privacy statement:** the authorization under which each identifier was enumerated, a note that no credentials were tested, and confirmation that out-of-scope PII was minimized/discarded. In an OSINT report this section is as important as the findings.

Frame the impact honestly: "these profiles exist" is Info; "this reused org handle plus this exposed secret enables takeover of a scoped asset," with the verified URLs and the chain spelled out, is the finding.

### Finding write-up template

A defensible maigret-sourced report separates *tool output* from *verified fact* from *impact*. A usable skeleton:

```
Title: Reused service handle "acme-ci" exposes a registerable namespace and a leaked token

Summary: The org's CI handle "acme-ci" is claimed on 6 platforms and UNCLAIMED on
  npm and Docker Hub, allowing namespace impersonation. One claimed profile
  (GitHub) links a gist containing a live-looking API token.

Scope & authorization: Enumeration of "acme-ci" is authorized under RoE §3 (org
  service accounts). No credentials were tested. Out-of-scope PII was discarded.

Method:
  maigret acme-ci --tags coding --keywords acme --json ndjson -n 25
  maigret --version -> <pinned>, DB auto-update: off, run date: 2026-11-15

Verified accounts (opened manually):
  - https://github.com/acme-ci  (avatar + org link confirm ownership)   [claimed]
  - https://hub.docker.com/u/acme-ci -> 404                              [UNCLAIMED]
  - https://www.npmjs.com/~acme-ci -> 404                                [UNCLAIMED]

Impact & chain:
  1. UNCLAIMED npm/Docker namespaces -> attacker registers them, publishes a
     typo/dependency-confusion package under the org's trusted handle.
  2. GitHub gist token -> [separate finding; verify validity per RoE before reporting].

Remediation:
  - Register/park the org handle on npm and Docker Hub.
  - Rotate the leaked token; add secret scanning to CI.
  - Enforce MFA on all service accounts sharing this handle.

Evidence attached: run.ndjson, report.html, screenshots of each verified URL.
```

Everything attacker-facing in that template is a *verified* URL or an *absence you confirmed*; the tool output is cited as method, not as the finding itself.

---

## References

- Maigret command-line options — https://maigret.readthedocs.io/en/latest/command-line-options.html
- Maigret features — https://maigret.readthedocs.io/en/latest/features.html
- Maigret FAQ — https://maigret.readthedocs.io/en/latest/faq.html
- Maigret library usage — https://maigret.readthedocs.io/en/latest/library-usage.html
- Maigret README (soxoj/maigret) — https://github.com/soxoj/maigret
- Maigret on PyPI — https://pypi.org/project/maigret/
- Maigret argparse source (flag spellings, defaults, `--id-type` choices) — https://raw.githubusercontent.com/soxoj/maigret/main/maigret/maigret.py

*Research supplement added 2026-11-15; every flag re-verified 2026-11-16 against the maigret ReadTheDocs command-line-options page and the `setup_arguments_parser()` argparse definition in `maigret/maigret.py` — `--permute`, `--keywords` (the `[++]` highlight), and `--graph`/`-G` are present in the source even though the options page omits them, and `--id-type` accepts `gaia_id`/`steam_id`/`vk_id`/`yandex_public_id`/`ok_id`/`orcid`/`qq_id`/`bilibili_id` among others. Defaults confirmed: `-n/--max-connections` 100, `--top-sites` 500, `--timeout` 30. Note: maigret exposes no `--verification` flag; database/result verification is `--self-check` (with `--diagnose`/`--auto-disable`) plus manual per-account confirmation (model: claude-opus-4-8).*
