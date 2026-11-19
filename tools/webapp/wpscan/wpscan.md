---
name: "wpscan"
categories:
  - corpus-webapp
homepage: "https://github.com/wpscanteam/wpscan"
origin: "corpus"
github-repo: "wpscanteam/wpscan"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-10-11"
meta-checked: "2026-11-09"
github-stars: 9843
github-pushed: "2026-11-07"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-09"
worth: 8.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 142
gh-activity-checked: "2026-11-09"
---

# wpscan

> A black-box WordPress scanner that fingerprints the core, themes and plugins and enumerates users, backups and known vulnerabilities.

## What it's for

Enumerates and attacks the WordPress attack surface of a site you are authorized to test: WordPress core version, installed plugins and themes (with versions), user accounts, config backup files, database exports and media listings. Reach for it whenever a target turns out to run WordPress and you need an inventory to feed further manual testing. It is a scanner and enumerator, not an exploiter — it reports versions and vulnerability references, and attacking what it finds is up to you. Vulnerability lookups require an API token from wpscan.com (25 free requests per day); without one it enumerates but does not match plugin/theme versions against a vulnerability database. Plugin and theme versions advertised in README/style files can be falsified by administrators, so cross-check anything version-critical.

## Typical use cases

### Run a default scan

Point it at a WordPress install with no extra options.

```
wpscan --url http://target.example.com/
```

Outcome: performs core version detection, theme detection and interesting-findings discovery with a speed/accuracy compromise, printing an inventory of the site's WordPress components plus any detected backup or disclosure findings — the starting inventory for every later, more targeted enumeration.

### Enumerate the default set

Ask for enumeration without specifying categories.

```
wpscan --url http://target.example.com/ --enumerate
```

Outcome: enumerates the default selection — vulnerable plugins (`vp`), vulnerable themes (`vt`), timthumbs (`tt`), config backups (`cb`), DB exports (`dbe`), backup folders (`bf`), users (`u`, IDs 1–10) and media (`m`, IDs 1–100) — reporting each category as it completes; combine specific codes instead when you want to skip the rest.

### Aggressively enumerate all plugins and themes

Force full detection when passive enumeration misses what is not linked.

```
wpscan --url http://target.example.com/ --enumerate ap,at --plugins-detection aggressive
```

Outcome: brute-forces known plugin and theme location paths (thousands of requests) instead of relying on passive clues, listing every plugin and theme found with location and version evidence; use when pages reference components that passive detection did not surface, and expect the scan to take markedly longer.

### Enumerate user accounts

List valid WordPress usernames for login attacks.

```
wpscan --url http://target.example.com/ -e u
```

Outcome: extracts user logins from the REST API, feeds and author archive pages (IDs 1–10 by default), printing each username with its ID; widen the range with `-e u1-100` to enumerate beyond the first ten accounts.

### Hunt vulnerable components, config backups and DB exports

Focus on the enumeration categories that most often yield direct leverage.

```
wpscan --url http://target.example.com/ --enumerate vp,vt,cb,dbe
```

Outcome: checks known-vulnerable plugin and theme lists plus wp-config backup files and database export files left in the web root, reporting each find with a location and any matched vulnerability title or reference — config backups and DB exports frequently contain credentials worth testing directly.

### Match versions against the vulnerability database

Add an API token to turn enumerations into vulnerability reports.

```
wpscan --url http://target.example.com/ --enumerate vp,vt --api-token <WPSCAN_API_TOKEN>
```

Outcome: for the WordPress core version and every enumerated plugin and theme, queries the WPScan vulnerability database (one API request per component) and prints matching CVEs and vulnerability references inline; without a token the same scan lists versions only. The token can also be supplied via the `WPSCAN_API_TOKEN` environment variable.

### Scan a self-signed HTTPS host without caching or a stable UA

Handle broken TLS and per-run freshness in one invocation.

```
wpscan --url https://target.example.com/ --disable-tls-checks --random-user-agent --clear-cache --enumerate ap,at
```

Outcome: skips certificate verification for hosts with self-signed or expired certificates, rotates the User-Agent per request to blunt trivial request fingerprinting, discards the local HTTP cache so results reflect the live site, and enumerates all plugins and themes.

---

## Bug bounty workflow with wpscan

**Scope first, always.** wpscan only belongs against a host that is explicitly in a program's scope and where automated scanning is permitted. Many programs forbid active scanners, brute force, or any traffic against login endpoints — read the rules of engagement before the first request, and if WordPress sits on a subdomain, confirm that subdomain (not just the apex) is in scope. wpscan's default scan already touches `wp-login.php`, `xmlrpc.php`, `/wp-json/wp/v2/users`, author archives, feeds, and `readme.html`; that is active, attributable traffic from your IP. `--password-attack`, `-e ap`/`-e at` (aggressive plugin/theme path brute-forcing), and anything on `xmlrpc.php` are the parts most likely to trip a rule or a WAF — never run them on spec.

The tool is an *enumerator with a vulnerability-lookup bolt-on*, not an exploiter. Its output is a hypothesis list (component + version + candidate advisory). Your job is to turn the strongest hypotheses into a validated, minimal finding — never to fire a full public exploit blind.

### Phase 0 — Confirm WordPress and set a quiet baseline

Confirm the target is actually WordPress before you point a WordPress scanner at it. Cheap passive tells: a `/wp-login.php` that returns the login form, `/wp-json/` returning JSON (the REST API root), `wp-content/`/`wp-includes/` asset paths in the HTML, a `<meta name="generator" content="WordPress X.Y">` tag, and `/readme.html`. Run the first pass deliberately slow and passive so you are not announcing yourself with a thread-storm:

```
wpscan --url https://target.example.com/ --detection-mode passive --throttle 500 --random-user-agent
```

`--detection-mode passive` restricts wpscan to clues already present in served pages (no path brute-forcing); `--throttle 500` puts 500 ms between requests and (per wpscan's own behaviour) forces `--max-threads` to 1 — a single-connection, low-rate scan. Record the detected core version and the generator string verbatim; this is your fingerprint baseline, and everything downstream is a hypothesis until corroborated.

### Phase 1 — Inventory the attack surface (still low-volume)

Enumerate the components that carry risk, but keep it to the categories that matter and stay passive on the first pass:

```
wpscan --url https://target.example.com/ --enumerate vp,vt,cb,dbe,u --plugins-detection passive --throttle 500
```

`vp`/`vt` check *known-vulnerable* plugins/themes only (a short, targeted list — far cheaper than `ap`/`at`); `cb`/`dbe` look for config backups and database exports in the web root; `u` enumerates user logins (IDs 1–10 by default). Passive plugin detection relies on what pages already reference, so you get the "linked" plugins without brute-forcing thousands of paths. This pass is your map: which components exist, which users exist, whether anything sensitive is sitting in the web root.

### Phase 2 — Turn versions into advisories (API token)

Enumeration without a token gives you versions but no vulnerability matching. Add a wpscan.com API token to query the WPVulnDB vulnerability database:

```
export WPSCAN_API_TOKEN=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
wpscan --url https://target.example.com/ --enumerate vp,vt --api-token "$WPSCAN_API_TOKEN"
```

wpscan spends **one API request per queried component** — one for the core version, one per plugin, one per theme. The free plan allows **25 API requests per day**; a site with 20 plugins can exhaust it in a single scan. See "API budget discipline" in Command recipes for how to stay under the cap, and note that when the quota is gone wpscan keeps scanning — it just stops annotating findings with vulnerabilities, silently, so a "clean" result late in the day may just be a spent quota.

### Phase 3 — Corroborate the fingerprint

Treat every version wpscan prints as a claim an administrator can lie about (see "Version fingerprinting limits" below). Before you build a finding on "plugin X 1.2.3 is vulnerable", confirm 1.2.3 independently: fetch the plugin's own `readme.txt`/`style.css` `Stable tag`/`Version` line, check asset `?ver=` query strings, and — where the program permits it — let wpscan's aggressive version detection compare file hashes rather than trust a self-reported string. Divergent evidence means the version is spoofed or the plugin is partially patched; do not report on the strength of a single spoofable string.

### Phase 4 — Validate one candidate, minimally

Pick the highest-impact, lowest-dupe candidate and prove *just enough*. For a reflected-XSS plugin advisory, that is a single request that pops a benign marker (`alert(document.domain)`), captured as request+response — not a weaponised payload. For an exposed `wp-config.php.bak`, it is the HTTP 200 and the first few bytes proving it is a PHP config, not the whole file. The evidence chain is always: **enumerated component + version → matching WPVulnDB advisory (ID/CVE) → one minimal proof the instance is actually affected.** Stop at proof. Running a public full-exploit PoC against a live bounty target is how hunters get removed from programs.

---

## Where the money is with wpscan

| Priority | Finding class | wpscan feature that reaches it | Typical severity | Dupe risk |
|---|---|---|---|---|
| 1 | Exposed DB export in web root (`dbe`) | `--enumerate dbe` | High–Critical (dumps user table, hashes, PII) | Low–Med |
| 2 | Exposed `wp-config` backup (`cb`) | `--enumerate cb` | High–Critical (DB creds, auth salts) | Low–Med |
| 3 | Known-vulnerable plugin, version-matched | `--enumerate vp --api-token` | Varies by advisory (up to Critical/RCE) | **High** (anyone can run the same scan) |
| 4 | Known-vulnerable theme | `--enumerate vt --api-token` | Varies by advisory | High |
| 5 | Backup folder / archive in web root (`bf`) | `--enumerate bf` | Med–High | Med |
| 6 | Timthumb-class file-fetch bugs (`tt`) | `--enumerate tt` | Med–High (legacy, rare today) | Low |
| 7 | User enumeration | `--enumerate u` (REST/author/feeds) | Low–Med (amplifier for auth attacks) | Very high |
| 8 | Weak credentials (only if in scope) | `--passwords --usernames --password-attack` | High–Critical | Med |
| 9 | Core version EOL / known-vulnerable | default scan + `--api-token` | Varies | High |

Read this table top-down on a fresh target. Rows 1–2 are the payouts least likely to be duplicates and least dependent on anyone's vuln feed — a config backup or SQL dump in the web root is a direct, demonstrable exposure. Rows 3–4 and 9 are real but **high dupe-risk**: the WPVulnDB match that wpscan hands you is the same match every other hunter's wpscan hands them, so speed and a crisp instance-specific PoC are what separate a paid report from a "duplicate". Row 7 (user enumeration) is almost never a standalone bounty — it is context that strengthens an auth finding; report it as an amplifier, not a headline.

---

## Detection modes and traffic cost

wpscan has a global `--detection-mode` (choices `mixed`, `passive`, `aggressive`; default `mixed`) and per-component overrides: `--plugins-detection`, `--plugins-version-detection`, `--themes-detection`, `--themes-version-detection`, and `--users-detection`, each taking the same three values. Understanding the cost difference is the difference between a polite scan and an accidental DoS.

| Mode | How it finds things | Traffic cost | When to use |
|---|---|---|---|
| `passive` | Reads only what served pages already reveal — HTML links, `?ver=` strings, generator tags, enqueued assets | A handful of requests | Default first pass; strict-scope or active-scan-forbidden programs |
| `mixed` (default) | Passive clues, then a bounded set of confirmation requests | Moderate | General use once you've confirmed scanning is allowed |
| `aggressive` | Brute-forces known paths: for `-e ap`/`at`, thousands of `/wp-content/plugins/<slug>/` and `/themes/<slug>/` probes; for version detection, fetches and hashes known release files | Hundreds to thousands of requests | Only when passive missed a component you have reason to believe exists, and the program tolerates it |

Two practical consequences. First, `--enumerate ap`/`at` *implies* aggressive-style path brute forcing — that is where the thousands of requests come from, not from `vp`/`vt`, which query a short known-vulnerable list. Second, aggressive *version* detection is the one mode that is hard for an administrator to defeat: it compares the hash of files served under `wp-includes`/plugin directories against hashes of known releases, so it corroborates a spoofed generator string. Use it narrowly and throttled (`--throttle`, low `-t`) when you need that corroboration, and never as an opening move.

Independent dials that cap the blast radius regardless of mode: `--throttle <ms>` (forces single-threaded), `-t`/`--max-threads` (default 5), `--request-timeout` (default 60 s), `--connect-timeout` (default 30 s), `--max-scan-duration <seconds>` (hard stop for a runaway scan), and `--scope <domains>` (confine the scanner to the exact hosts in your program scope — set this whenever the target links out, so wpscan never follows a redirect or asset reference off-scope).

---

## Version fingerprinting and its limits

wpscan reports a version for the core, each plugin, and each theme, and every downstream vulnerability match depends on those numbers. They are only as trustworthy as their source, and most sources are attacker-forgeable by the *site owner*:

- **Core version** comes from the `<meta name="generator">` tag, `readme.html`, feed `<generator>` elements, and `?ver=` query strings on core assets. Security/hardening plugins (WP Hide, Sucuri hardening, countless "hide my WP" plugins) strip or fake *all* of these. A missing or implausible version is itself a tell that hardening is present, not that the site is current.
- **Plugin/theme versions** come from the `Stable tag`/`Version` line of a readme/style file and from asset `?ver=` strings. An admin can edit these, and a partially-patched plugin may advertise an old version while running patched code (or vice versa).

The failure this produces is a rejected report: you cite "plugin X 1.2.3, vulnerable per WPVulnDB", the triager checks and the instance is 1.2.9 (the readme simply wasn't updated), and the report is closed as not-reproducible. Corroborate before you lean on a version, in rough order of strength:

1. **Hash-based (aggressive version detection):** fetch known release files and compare hashes — hardest to fake, since it reflects the bytes actually served.
2. **Multiple independent strings agreeing:** generator tag *and* `?ver=` *and* readme all saying the same version is far stronger than any one of them.
3. **Behavioural confirmation:** the minimal, benign PoC from Phase 4 — if the vulnerable code path actually responds as the advisory describes, the version question is moot. This is the gold standard and the thing a triager can reproduce.

If you cannot corroborate, say so in the report and downgrade confidence rather than asserting a version you only have one spoofable source for.

---

## Command recipes

Every recipe below is written for low-volume, authorized use. The two dials that keep you polite are `--throttle` (ms between requests; forces single-threaded) and `--max-threads` / `-t` (default 5); the default scan is *not* gentle.

**Quiet confirmation pass (passive, single-connection):**

```
wpscan --url https://target.example.com/ --detection-mode passive --throttle 750 --random-user-agent --format cli-no-color --output wpscan-passive.txt
```

Expected output: WordPress core version (with an "insecure"/EOL note if applicable), active theme, any passively-detected plugins, and interesting findings (headers, `xmlrpc.php` reachable, `wp-cron.php`, `readme.html`). `--throttle 750` ≈ one request per 0.75 s, single thread. `--output` saves a copy for the report; `--format cli-no-color` keeps the file free of ANSI escape codes.

**Targeted vulnerability pass under an API budget:**

```
wpscan --url https://target.example.com/ --enumerate vp,vt --api-token "$WPSCAN_API_TOKEN" --throttle 500
```

Expected output: for each known-vulnerable plugin/theme, a titled advisory block with the WPVulnDB reference, CVE (where assigned), affected/fixed versions, and reference URLs. `vp,vt` (vulnerable-only) keeps the API spend minimal — contrast with `ap`/`at`, which query *every* installed component and can blow the 25/day free cap in one run.

**API budget discipline.** Each queried component costs one request. To stay under the cap: scan vulnerable-only categories (`vp,vt`) not `ap,at`; let the HTTP cache do its job across re-runs (`--cache-ttl` defaults to 600 s — do *not* add `--clear-cache` unless you specifically need live data, since a cleared cache means a full re-query); and if you must enumerate all plugins, split work across days or request a higher-tier token. **When you hit the cap:** wpscan does not error out — it finishes enumerating and simply omits vulnerability data. Watch for the "API limit reached" notice in output; a late-in-the-day scan that shows components but no advisories is almost always an exhausted quota, not a clean site. Re-run the vuln lookup the next day, or on a component list you already have (`--plugins-list`/`--themes-list`) so you spend requests only on what you care about.

**Aggressive plugin/theme discovery (heavy — scope-gated):**

```
wpscan --url https://target.example.com/ --enumerate ap --plugins-detection aggressive --throttle 300 --max-threads 2
```

Expected output: plugins found by brute-forcing thousands of known `/wp-content/plugins/<slug>/` paths, including ones not referenced on any page. **Traffic cost: thousands of requests.** Only run this when passive detection missed something you have reason to believe is there, and only where the program tolerates active scanning. Even then, throttle it and drop the thread count — an unthrottled aggressive run is a self-inflicted mini-DoS and a fast route to a WAF ban or a program violation.

**Config-backup and DB-export sweep (high-value, low-volume):**

```
wpscan --url https://target.example.com/ --enumerate cb,dbe,bf --throttle 500
```

Expected output: any `wp-config.php.bak`/`.save`/`.swp`/`~`, `*.sql`/`dump.sql` in the web root, and common backup folders. These checks are a bounded list of path probes — cheap and worth running on every WordPress target. See Reporting evidence for how to prove one of these *without* exfiltrating its contents.

**User enumeration only:**

```
wpscan --url https://target.example.com/ -e u1-30
```

Expected output: logins for IDs 1–30 from the REST API (`/wp-json/wp/v2/users`), author archive redirects, and feeds. Widen the range only as needed; the default is `u1-10`.

**Credential testing — only with explicit written permission:**

```
wpscan --url https://target.example.com/ --usernames admin,editor --passwords ./small-list.txt --password-attack xmlrpc-multicall --throttle 1000 --max-retries 1
```

This is a brute-force attack: high-volume, lockout-triggering, and forbidden by most programs. `--password-attack` accepts `wp-login`, `xmlrpc`, or `xmlrpc-multicall`; the multicall variant packs up to `--multicall-max-passwords` (default 500) guesses into a single `xmlrpc.php` request, which is *faster* and therefore *noisier* and more likely to cause an outage. Do not run any of this unless the program explicitly authorizes credential attacks, and even then prefer a tiny, targeted list over a wordlist, throttle hard, and coordinate timing. When in doubt, don't.

**Self-signed / WAF-fronted hosts:**

```
wpscan --url https://target.example.com/ --disable-tls-checks --random-user-agent --detection-mode passive --throttle 750
```

`--disable-tls-checks` tolerates expired/self-signed certs; `--random-user-agent` (`--rua`) rotates the UA per request to blunt trivial UA-based blocking. The README also documents a `--stealthy` convenience switch that bundles passive detection with a randomised UA; the explicit flags above are the reliable, source-confirmed equivalent and let you also set the throttle.

---

## Pitfalls, noise and scope discipline

**The default scan is active, not passive.** New users assume "a scan" is read-only reconnaissance. wpscan's defaults brute-force, probe login endpoints, and hit `xmlrpc.php`. If the program forbids active scanning or automated tools, the *default invocation already violates it*. Start `--detection-mode passive` and escalate deliberately.

**`-e ap,at` is a request flood.** Aggressive all-plugins/all-themes enumeration fires thousands of path probes. Unthrottled, it can degrade a small site, light up every WAF rule, and get your IP (and your bounty account) blocked. Reserve it for when passive detection demonstrably missed something, and always pair it with `--throttle` and a low `--max-threads`.

**xmlrpc and login brute force get people banned.** `--password-attack` and anything repeatedly hitting `wp-login.php`/`xmlrpc.php` is credential-attack traffic. It triggers account lockouts (denying service to the real admin — itself a finding you *caused*, not found), fills logs, and is out of scope in most programs. Treat it as off-limits unless explicitly permitted in writing.

**Quota exhaustion masquerades as a clean result.** Once the 25/day free API cap is spent, wpscan keeps enumerating and silently stops matching vulnerabilities. A hunter who doesn't notice reports "no known vulnerabilities" when they simply ran out of lookups. Always check for the API-limit notice before trusting an empty vulnerability section.

**Version strings are spoofable.** Security plugins routinely hide or fake the core version, strip the generator tag, and rewrite `?ver=` strings. A report built on "version X is vulnerable" that the triager can't reproduce because the version was faked is a rejected report. Corroborate (next-to-last section) before you lean on a version.

**Enumerated ≠ exploitable.** `vp` means "this version appears in a vulnerability database", not "this instance is exploitable right now". The plugin may be deactivated, the vulnerable code path unreachable, or the issue already mitigated. Reporting the raw wpscan line without instance-specific proof is a low-quality, likely-duplicate, often-wrong report.

**Don't over-collect sensitive data.** Finding a DB export is the finding; downloading the whole dump is not — it is data you now hold that you were never authorized to possess, and it can breach the program's data-handling rules and the law. Prove exposure, then stop (see below).

**Cache vs freshness.** `--clear-cache` forces a full re-fetch *and* a full re-query of the API, burning quota. Leave the cache (default TTL 600 s) in place for iterative work; clear it only when you genuinely need live state.

---

## Reporting evidence

Capture the chain that makes the finding reproducible and the exposure undeniable — nothing more.

**For a version-matched vulnerability (vp/vt + API):**
- The exact wpscan invocation and the relevant output block: component slug, detected version, the **WPVulnDB reference and CVE**, affected/fixed version range, and the advisory URL.
- Your **independent version corroboration** (readme.txt/style.css line, `?ver=` string, or hash-based detection) — this pre-empts the "version was spoofed / already patched" rejection.
- One **minimal instance-specific PoC**: for XSS, a single request/response pair showing a benign `alert(document.domain)` marker reflected; for an auth-bypass, the single request that crosses the boundary. Never a weaponised or data-destructive payload.
- Impact framed against the advisory, mapped to the program's severity rubric.

**For an exposed config backup (`cb`) or DB export (`dbe`):**
- The URL wpscan reported and the raw request, plus the response **status line and `Content-Type`/`Content-Length` headers** proving it is served and sizeable.
- The **first few bytes** only — enough to prove the file type (`<?php` and a `DB_PASSWORD` *key name* for a config backup; a `-- MySQL dump` / `CREATE TABLE wp_users` header line for an export). **Redact any actual secret value.** Do not paste credentials, hashes, or user rows into the report.
- An explicit note that you did **not** download the full file, as a deliberate data-minimisation choice. State the impact by inference ("this file contains the live DB credentials / full user table") rather than by exfiltrating it to prove the point.

**For user enumeration (`u`):** the list of discovered logins and the source wpscan used (REST API / author archives / feeds), framed as an amplifier for the auth finding it supports — not as a standalone bug unless the program explicitly rewards it.

**Always include:** the full wpscan command line, the tool version, the timestamp, and your source IP (programs correlate scanner traffic). Save machine-readable output with `--format json --output finding.json` alongside the human-readable copy so the triager can parse exact versions and references.

---

## Coverage closeout — falsifiable claims

Treat coverage as claims you tried to *disprove*, not boxes you ticked. For each, record the invocation, the result, and the boundary you chose not to cross.

| Claim you tried to prove | Rejected (nothing to report) if |
|---|---|
| A config backup or DB export sits in the web root | `cb,dbe,bf` enumeration returns nothing on repeated, cache-cleared checks |
| An installed plugin/theme is at a known-vulnerable version | every version is corroborated (hash/behaviour) and matches no WPVulnDB advisory — *and your API quota was not already spent* |
| User logins are enumerable | REST API, author archives, and feeds all withhold logins (`-e u` returns nothing) |
| The core version is current and honestly reported | corroborated version matches no advisory and is a supported release |
| Credentials are weak | (only if in scope) a small, authorized guess list against a throttled attack yields no login |
| The login/xmlrpc surface is exposed | `xmlrpc.php` is disabled/filtered and `wp-login.php` enforces rate limiting |

The claim you usually cannot close from outside: whether an *enumerated* vulnerable component is actually reachable/exploitable in this deployment. Name that residual as "version-matched, instance not exploit-confirmed" rather than overstating it.

---

## Flag quick reference (safety-oriented)

```
--url <URL>                     # target; confirm it is in program scope first
--detection-mode passive        # start here: no path brute-forcing
--throttle <ms>                 # delay between requests; forces single thread
-t, --max-threads <n>           # default 5; lower it for politeness
--request-timeout <s>           # default 60
--connect-timeout <s>           # default 30
--max-scan-duration <s>         # hard stop for a runaway scan
--scope <domains>               # confine scanner to in-scope hosts
-e, --enumerate vp,vt,cb,dbe,bf,u,m   # default set; vp/vt are cheap, ap/at are heavy
--plugins-detection passive|mixed|aggressive   # aggressive = thousands of requests
--api-token <TOKEN>             # WPVulnDB lookups (env: WPSCAN_API_TOKEN); 25/day free
--random-user-agent, --rua      # rotate UA per request
--disable-tls-checks            # tolerate self-signed/expired certs
--cache-ttl <s>                 # default 600; leave cached to save API quota
--clear-cache                   # forces full re-fetch AND re-query (burns quota)
-f, --format cli-no-color|json  # machine-readable output for the report
-o, --output <file>             # save evidence
--passwords -P / --usernames -U / --password-attack wp-login|xmlrpc|xmlrpc-multicall
                                # brute force — scope-gated, explicit permission only
```

---

## References

- wpscan GitHub repo — https://github.com/wpscanteam/wpscan
- wpscan README (current master; API token, `WPSCAN_API_TOKEN`, 25 requests/day free plan, `--stealthy`) — https://raw.githubusercontent.com/wpscanteam/wpscan/master/README.md
- wpscan User Documentation wiki (enumeration codes, detection modes) — https://github.com/wpscanteam/wpscan/wiki/WPScan-User-Documentation
- wpscan enumeration CLI options (source of truth for `-e` codes and the `vp,vt,tt,cb,dbe,bf,u,m` default set, `u1-10`/`m1-100` ranges, `--plugins-detection`/`--themes-detection`/`--plugins-version-all` modes) — https://github.com/wpscanteam/wpscan/blob/master/app/controllers/enumeration/cli_options.rb
- CMSScanner core CLI options (source of truth for `--detection-mode` default `mixed`, `--throttle`, `--request-timeout` 60s, `--connect-timeout` 30s, `-t/--max-threads` 5, `--cache-ttl` 600s, `--clear-cache`, `--disable-tls-checks`, `--random-user-agent/--rua`, `--format`/`--output`/`--scope`) — https://github.com/wpscanteam/CMSScanner/blob/master/app/controllers/core/cli_options.rb
- wpscan vuln-API controller (`--api-token`, `--enterprise-db-token`, env vars) — https://github.com/wpscanteam/wpscan/blob/master/app/controllers/vuln_api.rb
- wpscan password-attack controller (`--passwords/-P`, `--usernames/-U`, `--password-attack` choices `wp-login`/`xmlrpc`/`xmlrpc-multicall`, `--multicall-max-passwords` default 500, `--login-uri`) — https://github.com/wpscanteam/wpscan/blob/master/app/controllers/password_attack.rb
- wpscan.com API / pricing (free-tier request cap) — https://wpscan.com/api

*Research supplement added 2026-11-15, grounded in the wpscan source controllers (enumeration, core, vuln_api, password_attack), the wpscan README/wiki, and CMSScanner core CLI options listed above (model: claude-opus-4-8).*
