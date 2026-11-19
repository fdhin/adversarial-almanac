---
name: "nuclei"
categories:
  - blackarch-scanner
version: "v3.11.1.r392.ge21e636"
homepage: "https://github.com/projectdiscovery/nuclei"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-10-31"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "projectdiscovery/nuclei"
github-stars: 32657
github-pushed: "2026-11-07"
github-archived: false
github-issues: 90
github-match: "exact"
gh-checked: "2026-11-07"
worth: 9.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 1187
gh-activity-checked: "2026-11-09"
---

# nuclei

> Fast, template-driven vulnerability scanner that runs community and custom YAML detection templates against URLs, hosts, networks and files.

## What it's for

nuclei is ProjectDiscovery's template-based vulnerability scanner. All detection logic lives in YAML templates — CVE checks, exposed login panels, misconfigurations, exposures, network probes — so a scan is just pointing the engine at targets and selecting templates by tag, severity, author, protocol type or file path. The engine speaks HTTP(S), DNS, raw TCP/SSL, file, headless-browser and code protocols, so web apps, network services and local artifacts can be covered in one pass. It detects, it does not exploit: a finding means a template matched, and template quality bounds result quality. The template library is the companion repo [[nuclei-templates]] (installed under `/usr/share/nuclei-templates` on distro packages); `nuclei -h` lists every flag.

## Typical use cases

### Scan a single target

Run the full default template set against one host.

```
nuclei -u https://example.com
```

Outcome: loads every template in the configured nuclei-templates directory, scans the target, and prints one colored `[template-id] [severity] [matched-url]` line per finding; add `-o findings.txt` to also write them to a file.

### Scan a list of targets with JSONL output

Bulk-scan from a file, one URL or host per line.

```
nuclei -l urls.txt -j -o findings.jsonl
```

Outcome: writes one JSON object per finding (template ID, severity, matched host, extracted values) to `findings.jsonl`, ready for `jq`, SIEM ingest or a tracking sheet.

### Only run high-impact severities

Filter templates by severity and keep output terse for CI or cron jobs.

```
nuclei -l urls.txt -severity critical,high -silent
```

Outcome: only templates whose severity is `critical` or `high` load; `-silent` suppresses banners and progress noise so only matching findings print.

### Select templates by tag or path

Narrow a scan to a class of checks.

```
nuclei -u https://example.com -tags cve,panel
nuclei -u https://example.com -t /usr/share/nuclei-templates/http/cves/ -t /usr/share/nuclei-templates/ssl
```

Outcome: the first command runs only templates tagged `cve` or `panel`; the second loads only templates under the given directories (`-tags` filters by metadata, `-t` by file path — combine with `-etags` to exclude a noisy tag).

### Run and validate your own template

Point nuclei at a template you wrote or copied.

```
nuclei -u https://example.com -t my-check.yaml
nuclei -t my-check.yaml -validate
```

Outcome: the first command executes the custom template against the target like any built-in; `-validate` parses and syntax-checks template files without scanning, which is the fast feedback loop while authoring.

### Execute chained workflows

Run workflow files that sequence multiple templates (for example detect a technology, then fire the product-specific CVE checks).

```
nuclei -l urls.txt -w /usr/share/nuclei-templates/workflows/
```

Outcome: nuclei executes each workflow — a YAML file that orchestrates other templates — against every target instead of the flat template list; use it when the bundled workflows match your stack.

### Tune rate and concurrency for large scans

Keep bulk scans polite and observable.

```
nuclei -l big-list.txt -rl 100 -c 25 -stats -o findings.txt
```

Outcome: caps requests at 100 per second (`-rl`) and 25 templates in parallel (`-c`), prints live scan statistics every few seconds (`-stats`), and appends findings to `findings.txt`.

### Refresh templates and run only the new ones

```
nuclei -ut
nuclei -l urls.txt -nt
```

Outcome: `-ut` updates the installed nuclei-templates library to the latest release; `-nt` restricts a scan to templates added in the most recent template release — a cheap way to re-skill an already-scanned list.

### Run only specific templates by ID

Fire one known check instead of a tag class.

```
nuclei -u https://target.example.com -id springboot-sbom
```

Outcome: only templates whose IDs match run against the target — comma-separate several IDs or use wildcards for families. The precision alternative to `-tags` when you know exactly which check applies, e.g. re-testing a single CVE after a patch.

### Automatic technology-aware scan

Let nuclei pick the template set from the detected stack.

```
nuclei -list urls.txt -as -jsonl -o auto.jsonl
```

Outcome: `-as` fingerprints each target with Wappalyzer technology detection and maps detected technologies to template tags, so only relevant templates run — more setup work per target than a fixed `-tags` selection, but far less noise on heterogeneous target lists. Results land as JSONL for downstream tooling.

### Run authenticated scans with a secrets file

Check endpoints behind a login.

```
nuclei -l urls.txt -sf secrets.yaml -jsonl -o auth.jsonl
```

Outcome: `-sf` loads a config file of secrets (cookies, headers, credentials) that templates reference for authenticated checks; header names in the file keep their exact casing for case-sensitive APIs, and `-ps` prefetches the secrets before the scan starts.

## Bug bounty workflow with nuclei

nuclei is a detection engine, not an exploit framework: a finding means *a template matched*, and the template library bounds result quality. On a bounty program that cuts two ways. The community templates are run by everyone the day a CVE drops, so anything they catch on a mature program is almost always a known dupe. The payouts live in the templates **you** author for behaviour nobody else is scanning for — a specific SaaS's forgotten debug route, a tenant-ID leak in a custom header, an OOB SSRF in an import feature. The workflow below is built around that: use the community set for a fast, polite baseline, then spend your time turning your own recon notes into private templates.

Everything here assumes an authorized program with written scope. nuclei can trivially exceed a program's rate rules or blast out-of-scope hosts, both of which get hunters **removed from programs**, so Phase 0 is not optional.

### Phase 0 — Program rules → a compliant config file

Read the program's rules of engagement first and translate them into a config file, not into flags you have to remember on every run. nuclei reads `$XDG_CONFIG_HOME/nuclei/config.yaml` (or `$NUCLEI_CONFIG_DIR/config.yaml`) automatically, so a per-program config is the safest way to make "polite" the default:

```yaml
# ~/.config/nuclei/example-program.yaml  — loaded with -config
rate-limit: 10            # -rl: ≤10 req/s total, under a typical "be gentle" clause
concurrency: 10           # -c: templates in parallel (default 25)
bulk-size: 10             # -bs: hosts in parallel per template (default 25)
timeout: 10
retries: 1
max-host-error: 30        # -mhe: drop a host after 30 network errors
header:
  - "X-Bug-Bounty: h1-yourhandle"   # many programs require an identifying header
disable-update-check: true
```

```
nuclei -l scope.txt -config ~/.config/nuclei/example-program.yaml -severity critical,high,medium
```

Key facts that keep you compliant:

- **`-rl` (`-rate-limit`, default 150/s) is the hard ceiling.** The docs state the request rate cannot exceed `-rl` *regardless of* `-c` and `-bs`. So you tune throughput with one knob and leave concurrency for CPU/memory tuning. `-rld` (`-rate-limit-duration`, default `1s`) is the window. `-rlm` (`-rate-limit-minute`) is **deprecated** — don't base new configs on it.
- A program that says "1 request/second" means `-rl 1`. A program that forbids automated scanning entirely means **do not run nuclei at all** — there is no polite setting for a prohibition.
- Pin scope. nuclei happily follows a list into out-of-scope territory. Keep `scope.txt` to resolved in-scope hosts only, and use `-eh` (`-exclude-hosts`) for carve-outs. Avoid `-sa` (`-scan-all-ips`) unless every resolved IP is in scope — it fans a hostname out to all its A/AAAA records.
- Identify yourself. Set the program's required marker (`X-Bug-Bounty`, `X-HackerOne-Research`, a tagged User-Agent) via the `header:`/`-H` field so the blue team can distinguish your traffic from a real attack.

### Phase 1 — Recon intake: subdomain enum → nuclei

nuclei takes a flat list on `-l`, so it drops in at the end of any recon pipeline. The canonical ProjectDiscovery chain resolves and filters before anything is scanned, which is itself a scope-safety control:

```
subfinder -d example.com -silent \
  | dnsx -silent \
  | httpx -silent -mc 200,301,302,401,403 \
  | nuclei -config ~/.config/nuclei/example-program.yaml -j -o run1.jsonl
```

`httpx` in the middle matters: it drops dead hosts so nuclei never wastes its per-host error budget (`-mhe`) on them, and its `-mc` filter keeps you off hosts that only answer on unexpected codes. nuclei can also read richer inputs directly with `-im` (`-input-mode`): `burp` (an exported Burp XML), `openapi`/`swagger` (scan from a spec — pairs naturally with an API target), `jsonl`, and `yaml`. Feeding it an OpenAPI file turns the schema into a target list, which is the nuclei analogue of the OpenAPI mining in an API methodology.

### Phase 2 — Low-impact baseline pass

Before anything intrusive, run a pass that is almost all GET-and-read: technology detection, exposures, misconfigurations, default logins you only *detect*, info disclosures. Use `-as` (`-automatic-scan`) to let nuclei fingerprint each host with Wappalyzer tech detection and map it to template tags — far less noise on a heterogeneous list than firing the whole set at every host:

```
nuclei -l live.txt -as -severity info,low,medium -j -o baseline.jsonl -stats
```

`-stats` prints live progress so you can see the request volume you are generating in real time — if the rate looks wrong, kill it before the program notices. This pass produces your target map: which host runs what, which panels are exposed, where the info leaks are. Most of it is low-severity or dupe-prone, but it tells you where to point the custom templates in Phase 4.

### Phase 3 — Tag and severity triage

Template selection is the whole game. The filters, in order of how often you reach for them:

| Filter | Flag | Use |
|---|---|---|
| By tag | `-tags cve,panel,exposure` | class of check |
| Exclude tag | `-etags fuzz,dos,intrusive` | strip noisy/destructive classes |
| Force-include an ignored tag | `-itags` | run a tag that's on the default `.nuclei-ignore` list |
| By severity | `-s critical,high` / `-es info` | triage by impact |
| By path | `-t http/cves/2024/ -t http/exposures/` | directory of templates |
| By exact ID | `-id CVE-2024-1234` | re-test one check (e.g. after a patch) |
| By protocol type | `-pt http` / `-ept dns,ssl` | restrict to one protocol |
| By expression | `-tc "contains(tags,'cve') && cvss_score>=7"` | metadata query across all templates |

`-tc` (`-template-condition`) is the power tool: it filters on template *metadata* with a DSL expression, so you can load, say, only CVEs with a high CVSS or a high EPSS score — exactly the templates most likely to still be unpatched on a given target. Some tags (`fuzz`, `dast`, intrusive checks) are excluded by default; fuzzing templates require `-dast` (the old `-fuzz` flag is deprecated), headless templates require `-headless`, and code-protocol templates require `-code`. Nothing destructive runs unless you ask for it by name.

### Phase 4 — Authoring custom templates (the differentiator)

This is where bounty value comes from. A template is one YAML file; `-validate` syntax-checks it without sending a request, which is your fast authoring loop. Here is a complete, realistic template for a bug the community set will *not* have: a fictional "Acme Deploy" dashboard that exposes an unauthenticated build-config endpoint leaking an internal registry token.

```yaml
id: acme-deploy-config-exposure

info:
  name: Acme Deploy — Unauthenticated Build Config Exposure
  author: yourhandle
  severity: high
  description: |
    Acme Deploy exposes /api/v2/_internal/buildconfig without authentication,
    leaking the internal container-registry URL and a long-lived push token.
  reference:
    - https://example.com/acme-deploy/docs/api
  classification:
    cwe-id: CWE-200          # Exposure of Sensitive Information
    cvss-metrics: CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:N/A:N
    cvss-score: 7.5
  metadata:
    verified: true
    max-request: 2
    shodan-query: 'http.title:"Acme Deploy"'
  tags: acme,exposure,config,token

http:
  - method: GET
    path:
      - "{{BaseURL}}/api/v2/_internal/buildconfig"
    # reuse cookies across steps is on by default; this is a single unauth GET
    matchers-condition: and
    matchers:
      - type: status
        status:
          - 200
      - type: word
        part: header
        words:
          - "application/json"
      - type: word
        part: body
        words:
          - '"registry_url"'
          - '"push_token"'
        condition: and
      - type: dsl
        dsl:
          - 'len(body) < 8192'     # a real config, not a catch-all 200 page
    extractors:
      - type: json
        part: body
        name: push_token
        json:
          - '.push_token'
      - type: regex
        part: body
        group: 1
        regex:
          - '"registry_url"\s*:\s*"(https?://[^"]+)"'
```

Walking the parts that make it a *good* template rather than a false-positive generator:

- **`matchers-condition: and`** sits beside `matchers:` and requires every matcher to pass. The default is `or` (any one matches), which is how beginners ship templates that fire on every 200 page. `condition:` *inside* a single matcher combines its own `words`/`dsl`/`regex` entries the same way (default `or`).
- **Layered matchers beat one broad one.** Status 200 alone is worthless; `+ Content-Type json + two required body keys + a size sanity check` is what separates the real endpoint from a SPA that returns 200 for everything. `part:` selects where to look — `body` (the default), `header`, `all`, `raw`; DSL exposes `status_code`, `content_length`, `all_headers`, `body`, `raw`, and per-header variables (lowercased, `-`→`_`, e.g. `content_type`).
- **The seven matcher types:** `status`, `size`, `word`, `regex`, `binary` (hex signatures, e.g. `504B0304` for a ZIP), `dsl` (expression), `xpath`. `negative: true` inverts one (match when something is *absent* — great for "missing security header"); `internal: true` keeps a matcher's result out of output, used to gate multi-step `flow`s.
- **Extractors turn a match into evidence.** `json` uses jq-style expressions, `regex` with `group: 1` pulls one capture, `kval` grabs a header/cookie by name, `xpath` (optionally `attribute:`), `dsl` computes. A `name`d, non-`internal` extractor prints its value next to the finding — that extracted `push_token` prefix *is* your PoC. Mark an extractor `internal: true` instead when you only want to feed the value into a later request.
- **`classification` + `metadata`** are not decoration. `cvss-score`/`cvss-metrics`/`cve-id`/`cwe-id` let `-tc` select your template later and tell the triager you did the severity math; `max-request` documents the request budget; `shodan-query` (an uncover metadata field) can seed target discovery.

Chaining across steps is where private templates get sharp. Use **raw requests** (a `raw:` list of literal HTTP blocks) when you need a login step to feed a protected step. An `internal: true` extractor on step 1 becomes a `{{name}}` variable in step 2, and DSL matchers can reference prior responses by numeric suffix (`status_code_1`, `body_2`):

```yaml
http:
  - raw:
      - |
        POST /login HTTP/1.1
        Host: {{Hostname}}
        Content-Type: application/x-www-form-urlencoded

        user={{username}}&pass={{password}}
      - |
        GET /api/v2/_internal/buildconfig HTTP/1.1
        Host: {{Hostname}}
        Authorization: Bearer {{auth}}
    extractors:
      - type: regex
        name: auth          # captured from step 1, injected into step 2
        internal: true
        group: 1
        regex:
          - '"access_token"\s*:\s*"([^"]+)"'
    matchers:
      - type: dsl
        dsl:
          - 'status_code_2 == 200 && contains(body_2, "push_token")'
```

Note the standard variables available in paths and raw requests: `{{BaseURL}}` (full input URL), `{{RootURL}}` (scheme+host+port), `{{Hostname}}` (host:port), `{{Host}}`, `{{Port}}`, `{{Path}}`, `{{File}}`, `{{Scheme}}`. Redirects are **not** followed by default — add `redirects: true` and cap with `max-redirects` when a flow needs them.

### Phase 5 — OOB / blind detection with interactsh

The bugs with no response-body tell — blind SSRF, blind XXE, some RCE, DNS-exfil — need an out-of-band collaborator. nuclei ships interactsh correlation built in: put `{{interactsh-url}}` anywhere in an HTTP or network request and nuclei substitutes a unique callback URL, then correlates any hit back to the exact template and request automatically.

```yaml
id: acme-import-blind-ssrf

info:
  name: Acme Deploy — Blind SSRF in URL importer
  author: yourhandle
  severity: high
  tags: acme,ssrf,oob
  metadata:
    max-request: 1

http:
  - raw:
      - |
        POST /api/v2/import HTTP/1.1
        Host: {{Hostname}}
        Content-Type: application/json

        {"source_url": "https://{{interactsh-url}}/probe"}
    matchers-condition: and
    matchers:
      - type: word
        part: interactsh_protocol   # dns | http | smtp
        words:
          - "dns"                   # DNS hit alone proves the server resolved our host
      - type: regex
        part: interactsh_request    # the raw callback the server made
        regex:
          - 'probe'
```

Why `dns` is the first matcher: a DNS interaction proves the target *resolved* your host — the minimal, least-intrusive proof of SSRF — without you ever fetching an internal resource. `http` means it actually made the request; `interactsh_request`/`interactsh_response` carry the raw callback data for your report.

For bounty work, prefer a **self-hosted interactsh server** over the shared public defaults (`oast.pro,oast.live,oast.site,oast.online,oast.fun,oast.me`): public OAST domains are widely blocklisted by WAFs (killing your detection) and you don't control the interaction logs that become your evidence. Stand up your own and point nuclei at it:

```
# on a box with a domain whose NS records you control:
interactsh-server -domain oob.example.com -ip 198.51.100.10 \
  -hostmaster admin@example.com -token "$ISH_TOKEN"

# then, client-side:
nuclei -l live.txt -tags ssrf,oob \
  -iserver https://oob.example.com -itoken "$ISH_TOKEN"
```

Relevant flags: `-iserver`/`-interactsh-server`, `-itoken`/`-interactsh-token` (auth for your self-hosted instance), the `-interactions-*` tuning knobs (`-interactions-poll-duration` default 5s, `-interactions-cooldown-period` default 5s, `-interactions-cache-size` default 5000), and `-ni`/`-no-interactsh` to disable OAST entirely (which also *excludes* OAST-based templates — useful when a program forbids third-party callbacks).

### Phase 6 — Authenticated scans

Most of the attack surface is behind a login, and nuclei applies secrets from a `-sf` (`-secret-file`) YAML to matching HTTP requests. Two section types: `static` (you supply the material) and `dynamic` (nuclei logs in via a template first, then reuses the result). Scope each secret with `domains` (exact) or `domains-regex` (mutually exclusive; `domains` wins on a tie; `.*` = all targets):

```yaml
static:
  - type: header
    domains:
      - api.example.com
    headers:
      - key: X-Api-Key
        value: REDACTED_KEY

  - type: cookie
    domains-regex:
      - ".*\\.example\\.com"
    cookies:
      - key: session
        value: REDACTED_SESSION

  - type: bearertoken
    domains:
      - app.example.com
    token: REDACTED_JWT

dynamic:
  - template: login.yaml          # a template whose extractors emit the cookie
    variables:
      - key: username
        value: hunter@example.com
      - key: password
        value: REDACTED
    input: https://app.example.com
    type: cookie
    domains:
      - app.example.com
    cookies:
      - raw: "{{session-cookie}}"  # value produced by login.yaml's extractor
```

The exact static `type` values are lowercase: **`basicauth`** (`username`/`password`), **`header`** (`headers:` list), **`cookie`** (`cookies:` list, or a `raw:` string), **`bearertoken`** (`token:`), **`query`** (`params:` list). Run with `-sf secrets.yaml`; add `-ps` (`-prefetch-secrets`) to resolve dynamic logins once before the scan rather than lazily. Operational notes from the docs: secrets apply to **HTTP requests only** (headless templates never receive them — log in inside the template and pass `-var`); OAuth/PKCE redirect flows can't be driven by nuclei, so grab a token out-of-band and drop it in as a `bearertoken`/`cookie` static secret; the file can be SOPS-encrypted (`encrypted_regex: "^(password|username|token|value|key|raw)$"`), which is the right way to keep live credentials out of plaintext. A per-template `skip-secret-file: true` (v3.3.1+) opts a template out of injection.

### Phase 7 — Differential re-runs

Scanning the same program every week is wasted traffic if you re-fire the whole set. Two mechanisms cut it to just-the-new:

```
nuclei -ut                                   # update the template library
nuclei -l scope.txt -nt -config ~/.config/nuclei/example-program.yaml -j -o week2.jsonl
```

`-nt` (`-new-templates`) runs **only templates added in the latest nuclei-templates release**, and `-ntv`/`-new-templates-version` targets a specific version's additions. This is how you win the race on a freshly disclosed CVE: `-ut` then `-nt` re-skills an already-mapped target list for a fraction of the requests, and the first valid hit on a new check is the least-dupe-prone finding on the whole program. Keep your JSONL outputs per run and diff them (`jq -s` on `template-id`+`matched-at`) to surface what changed on the *target* between runs — a newly exposed endpoint is as much a finding as a new template. Pair `-duc` (`-disable-update-check`) in the config with an explicit, logged `-ut` so template updates are a deliberate step you can attribute, not a silent change mid-engagement.

### Phase 8 — From match to reportable finding

A nuclei match is a *lead*. Before it becomes a report: re-run the single template with `-id <id> -debug` (or `-dreq -dresp`) to capture the exact request/response, reproduce it by hand with `curl` to prove it isn't a scanner artifact, and confirm impact (does the leaked token actually authenticate? does the SSRF reach something?). A template firing is never, by itself, the finding.

---

## Where the money is with nuclei

Priority is a function of **dupe risk**, not just severity: on a mature program the community templates have already been run by hundreds of hunters, so the high-value lane is custom detection.

| Priority | Finding class | How you reach it | Typical severity | Dupe risk |
|---|---|---|---|---|
| 1 | Custom-template logic bug (per-app exposure, tenant/token leak) | Author your own template (Phase 4) | High–Critical | **Low** — nobody else scans for it |
| 2 | Blind SSRF / OOB RCE | `{{interactsh-url}}` + self-hosted interactsh (Phase 5) | High–Critical | Low–Medium |
| 3 | Fresh CVE on day of disclosure | `-ut` then `-nt` race (Phase 7) | High–Critical | Medium (speed-dependent) |
| 4 | Auth-gated exposure behind login | `-sf` secrets file (Phase 6) | Medium–High | Low–Medium |
| 5 | Exposed panels / default creds (detect-only) | `-tags panel,default-login` | Medium | **High** |
| 6 | Known CVEs, community templates | `-tags cve -s critical,high` | High | **Very high** on mature programs |
| 7 | Misconfig / exposures (`.git`, `.env`, backups) | `-tags exposure,config` | Low–Medium | High |
| 8 | Info disclosure, missing headers, tech fingerprint | `-as`, `-s info,low` | Info–Low | Very high |

The inversion versus a generic scanner: the community CVE set (rows 6–8), which is what most people *think* nuclei is for, is the most duped surface on the program. Rows 1–3 — your own templates, OOB, and the new-template race — are where nuclei actually pays.

---

## Command recipes

Each block is a real invocation with the low-volume settings called out.

**Polite baseline from a recon pipeline.**
```
subfinder -d example.com -silent | dnsx -silent | httpx -silent -mc 200,301,302,401,403 \
  | nuclei -rl 10 -c 10 -bs 10 -mhe 30 -H "X-Bug-Bounty: h1-yourhandle" \
           -severity critical,high,medium -j -o baseline.jsonl -stats
```
Expected: JSONL, one object per finding (`template-id`, `matched-at`, `info.severity`, extracted values). `-rl 10` caps total throughput at 10 req/s no matter what `-c`/`-bs` are; `-stats` shows live request volume so you can abort if it climbs.

**Tech-aware scan on a heterogeneous list (less noise).**
```
nuclei -l live.txt -as -etags dos,intrusive,fuzz -rl 20 -j -o auto.jsonl
```
Expected: Wappalyzer fingerprints each host, only matching-tag templates run; `-etags` strips destructive classes defensively.

**High-value CVEs only, by metadata query.**
```
nuclei -l live.txt -tc "contains(tags,'cve') && cvss_score>=8.0" -rl 15 -j -o critcve.jsonl
```
Expected: loads only templates whose metadata satisfies the expression — the unpatched-likely, high-impact subset.

**Validate and dry-run a custom template before it ever touches the target.**
```
nuclei -t acme-deploy-config-exposure.yaml -validate      # syntax only, no requests
nuclei -u https://staging.example.com -t acme-deploy-config-exposure.yaml -debug
```
Expected: `-validate` reports parse errors and exits; `-debug` (or `-dreq`/`-dresp`) prints the full request/response for evidence capture.

**OOB SSRF sweep against a self-hosted collaborator.**
```
nuclei -l live.txt -tags ssrf,oob -iserver https://oob.example.com -itoken "$ISH_TOKEN" \
  -rl 10 -j -o ssrf.jsonl
```
Expected: `{{interactsh-url}}` callbacks land on your server; a DNS interaction alone confirms SSRF. Use `-ni` instead if the program bans third-party callbacks.

**Authenticated scan.**
```
nuclei -l app-urls.txt -sf secrets.yaml -ps -rl 10 -j -o auth.jsonl
```
Expected: secrets injected into matching-domain requests; `-ps` resolves the dynamic login once up front.

**Differential re-run after a template release.**
```
nuclei -ut && nuclei -l scope.txt -nt -rl 10 -config ~/.config/nuclei/example-program.yaml -j -o $(date +%F).jsonl
```
Expected: only newly-added templates run — cheap re-skilling of a mapped list; the day-one hit is the low-dupe one.

**Single-ID re-test after a fix (patch verification).**
```
nuclei -u https://app.example.com -id acme-deploy-config-exposure -debug
```
Expected: one template, full I/O — confirm the issue is gone (or isn't) with a copy-pasteable transcript.

A note on scan strategy for larger in-scope lists: `-ss host-spray` (vs. the default `template-spray`) is the memory-safer choice the docs recommend for mass scanning; keep `-rl` low regardless, and never raise `-rsr`/`-response-size-read` (default 4MB) hunting for data in bodies — it multiplies memory by concurrency.

---

## Pitfalls, noise and scope discipline

How hunters get reports rejected — or get removed from programs — with nuclei, and the setting that prevents each:

- **Blasting past the program's rate rule.** The default `-rl` is **150 req/s** and default `-c`/`-bs` are 25 each — far above what most programs tolerate. A single unthrottled `nuclei -l big-list.txt` looks exactly like an attack. *Fix:* a per-program config with `rate-limit` set to the program's cap; remember `-rl` is the real ceiling, `-c`/`-bs` are not throughput controls.
- **Scanning out of scope.** nuclei scans whatever is in the list; `-sa` (`-scan-all-ips`) and loose wildcards drag in hosts the program never authorized. Out-of-scope traffic is a fast ban. *Fix:* resolve and filter to in-scope hosts in the pipeline (`httpx` gate), use `-eh` for carve-outs, don't use `-sa` unless every IP is in scope.
- **Reporting raw scanner output as findings.** A template match is not a validated vulnerability. Submitting unverified `-silent` output — especially dupe-prone community CVE hits — is how triagers learn to downgrade your reports. *Fix:* reproduce by hand (Phase 8), confirm impact, attach the request/response.
- **Dup-flooding with the community set.** Running the full CVE/exposure set at a mature program produces a pile of known-issue matches that are all duplicates. *Fix:* invert the priority table — lead with custom templates, OOB, and `-nt`.
- **Destructive/DoS templates.** Some checks are fuzzing or resource-exhaustion class. They don't run by default (fuzzing needs `-dast`, intrusive tags are `.nuclei-ignore`'d), but `-itags`/`-include-templates` can re-enable them. *Fix:* keep `-etags dos,intrusive,fuzz` unless the program explicitly permits, and never raise payload volume (`-pc`) on state-changing endpoints.
- **Public OAST domains blocked.** The shared `oast.*` servers are WAF-blocklisted, so OOB checks silently fail (no hit ≠ not vulnerable). *Fix:* self-host interactsh (`-iserver`/`-itoken`).
- **Leaking live credentials.** A plaintext `-sf` file in a repo or screenshot is a credential spill. *Fix:* SOPS-encrypt the secret file; scope every secret with `domains`/`domains-regex` so it never rides to the wrong host.
- **Silent template updates mid-engagement.** Auto-update can change your detection set between runs, making results non-reproducible. *Fix:* `-duc` in the config + an explicit, logged `-ut` when you choose to update.
- **False positives from lazy matchers.** A `status: 200`-only matcher fires on SPA catch-alls. *Fix:* `matchers-condition: and` with layered matchers and a size/content-type sanity check, as in Phase 4.

---

## Reporting evidence

What to capture so a finding stands on its own:

- **The exact template**, inline in the report (or linked). Since custom templates are your edge, include the YAML so the triager can reproduce deterministically. Add `-ot` (`-omit-template`) only to *console* output, never to the report.
- **The request/response pair**, captured with `-debug` or `-dreq`/`-dresp`, or `-sresp`/`-store-resp` to write full responses to disk. This is the proof the match wasn't a scanner artifact.
- **The extracted values** — the `name`d extractor output (a token prefix, a leaked `registry_url`, a version string) *is* the evidence. Redact the sensitive part; show enough to prove access, never exfiltrate more than the PoC needs.
- **For OOB findings:** the `interactsh_protocol` that fired and the raw `interactsh_request`/`interactsh_response`, plus your self-hosted server's interaction log with timestamps correlating the callback to your probe.
- **The JSONL line** (`-j`) for the finding — it carries `template-id`, `matched-at`, `info` (name/severity/classification), `curl-command` (nuclei emits a reproducing curl), and timestamps (`-ts`).
- **A hand-run `curl` reproduction** independent of nuclei, proving the bug exists without the tool.
- **Scan hygiene metadata:** the `-rl`/`-c`/`-bs` you used and your identifying header, demonstrating you stayed within program rules — triagers notice, and it protects you if the blue team asks about the traffic.
- **Impact confirmation:** one line showing the leaked/blind primitive actually does something (the token authenticates, the SSRF reaches an internal host). Map severity to the program's rubric and cite the `classification` CWE/CVSS you set.

---

## References

- nuclei — running / CLI usage — https://docs.projectdiscovery.io/tools/nuclei/running
- nuclei — mass scanning CLI (rate/concurrency/strategy, polite scanning) — https://docs.projectdiscovery.io/tools/nuclei/mass-scanning-cli
- nuclei — authenticated scans / secret file format — https://docs.projectdiscovery.io/tools/nuclei/authenticated-scans
- Templates — introduction — https://docs.projectdiscovery.io/templates/introduction
- Templates — structure (info/metadata keys) — https://docs.projectdiscovery.io/templates/structure
- Templates — HTTP protocol (requests, variables, raw) — https://docs.projectdiscovery.io/templates/protocols/http/basic-http
- Templates — matchers reference — https://docs.projectdiscovery.io/templates/reference/matchers
- Templates — extractors reference — https://docs.projectdiscovery.io/templates/reference/extractors
- Templates — out-of-band (interactsh) testing — https://docs.projectdiscovery.io/templates/reference/oob-testing
- Nuclei-interactsh integration — https://blog.projectdiscovery.io/nuclei-interactsh-integration/
- Scanning login-protected targets with Nuclei v3.2 — https://projectdiscovery.io/blog/scanning-login-protected-targets-with-nuclei
- Nuclei Templates v9.5.0 (classification/metadata enrichment) — https://projectdiscovery.io/blog/nuclei-template-v9-5-0-update
- Interactsh (self-hosting) — https://github.com/projectdiscovery/interactsh
- Companion template library — [[nuclei-templates]]

*Research supplement added 2026-11-15, grounded in projectdiscovery.io/docs (running, mass-scanning-cli, authenticated-scans, templates: introduction/structure/basic-http/matchers/extractors/oob-testing) and ProjectDiscovery blog posts, verified against nuclei v3.11.x flag reference (model: claude-opus-4-8).*
