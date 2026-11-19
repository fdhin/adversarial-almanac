---
name: "paramspider"
categories:
  - blackarch-webapp
version: "v1.0.1.r3.gc44bdaa"
homepage: "https://github.com/devanshbatham/ParamSpider"
packaged-by: [blackarch]
blackarch-builddate: "2026-03-07"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "devanshbatham/ParamSpider"
github-stars: 3201
github-pushed: "2026-03-07"
github-archived: false
github-issues: 40
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# paramspider

> Mines parameterized URLs for a domain from web archives, ready for fuzzing and further probing.

## What it's for

ParamSpider fetches URLs related to a domain from Wayback Machine archives and filters out "boring" URLs — ones without parameters and static assets — leaving the parameterized endpoints that matter for bug hunting and fuzzing. Output uses a placeholder value (default `FUZZ`) in place of each parameter value, so results drop straight into fuzzers. Pure Python 3; takes a single domain or a list.

## Typical use cases

### Harvest parameterized URLs for a domain

```
paramspider -d example.com
```

Outcome: pulls archived URLs for the domain, keeps the parameterized ones, and writes them out with parameter values replaced by `FUZZ`.

### Stream results to the terminal

```
paramspider -d example.com -s
```

Outcome: discovered URLs print to the terminal as they are found rather than only landing in the output file.

### Process many domains from a file

```
paramspider -l domains.txt
```

Outcome: runs the same archive mining for every domain in the list.

### Replace values with a custom probe string

```
paramspider -d example.com -p '"><h1>reflection</h1>'
```

Outcome: parameter values are set to the given string instead of `FUZZ`, preloading the URL list for reflection-based XSS triage.

### Route archive requests through a proxy

```
paramspider -d example.com --proxy 127.0.0.1:7890
```

Outcome: the web archive lookups are sent through the specified proxy.

No corpus evidence — derived from upstream documentation.

---

## Bug bounty workflow with paramspider

ParamSpider occupies one narrow, valuable slot in a bug-bounty pipeline: it turns a domain name into a list of historically-seen **parameterized** URLs **without sending a single packet to the target**. Every request it makes goes to `web.archive.org`'s CDX index, never to the program's asset. That makes it a Phase-0/recon tool you can run before you have confirmed scope rules on active scanning — the archive query is passive reconnaissance against a third-party public dataset, not traffic against the target. The findings come later, downstream, when you feed its output into a fuzzer that *does* touch the target. Keep that split clear in your head: **paramspider = passive discovery; dalfox/nuclei = active validation.** The rate discipline and scope rules bite at the second stage, not the first.

Understand what the current tool actually is before building a workflow on it. The 1.0.x rewrite (Debian/BlackArch `1.0.1`) is deliberately minimal — **five flags, no more**: `-d/--domain`, `-l/--list`, `-s/--stream`, `-p/--placeholder`, `--proxy`. Static-asset exclusion is **hardcoded**, not a flag; subdomain expansion, output-path control, and recursion depth that older versions had are **gone**. Any tutorial telling you to run `paramspider -d example.com --exclude php,jpg -o out.txt --level high` is describing a tool that no longer exists (see Pitfalls). Build on the five real flags and do everything else — filtering, dedup, classification, diffing — with standard Unix tooling and the tomnomnom/s0md3v pipeline.

### Phase 0 — Define the archive scope, confirm no-contact

Decide what you are asking the archive for. `paramspider -d example.com` queries the CDX index for `url=example.com/*` — that is the **apex host and its paths only**. It does **not** include subdomains (the old `--subs` behaviour was removed). If your program scope is `*.example.com`, you must enumerate subdomains first and feed them as a list:

```
subfinder -d example.com -silent -o subs.txt
paramspider -l subs.txt
```

This is also a scope-safety control: an over-broad `-l` list that includes a subdomain you are *not* authorized to test will produce URLs you must then discipline yourself not to fuzz. Trim the list to in-scope hosts **before** paramspider, not after — it is cheaper to filter the input than to filter thousands of output URLs.

### Phase 1 — Harvest the archived parameter surface

```
paramspider -d example.com
# -> [INFO] Saved cleaned URLs to results/example.com.txt
```

Output lands in `results/<domain>.txt` relative to your current directory, one URL per line, with every parameter **value** replaced by the placeholder (`FUZZ` by default) and parameter **names** preserved. Only URLs containing `?` survive; the 17 hardcoded static extensions (`.jpg .jpeg .png .gif .pdf .svg .json .css .js .webp .woff .woff2 .eot .ttf .otf .mp4 .txt`) are dropped before rewriting. For a list run, each domain writes its own `results/<domain>.txt`.

Treat this file as **raw material, not a finding**. Archived URLs are historical — many endpoints 404 today, parameters have been renamed, and the archive is months to years stale. The value is the *parameter names* and *endpoint shapes* that a live crawl would never surface because the links no longer exist on the site.

### Phase 2 — Hygiene: dedup, normalize, strip the noise the tool missed

ParamSpider dedups by exact URL string and collapses CDX url-keys, but it does **not** normalize away near-duplicates (same endpoint, different cache-buster), nor does it exclude every static type (hardcoded list misses `.ico .map .scss .less .avif` and more). Clean the set before it ever reaches a fuzzer:

```
# collapse near-duplicate parameterized URLs (same path+param set)
uro -i results/example.com.txt -o params_clean.txt

# drop static extensions the hardcoded list missed
grep -ivE '\.(ico|map|scss|less|avif|woff2?|ttf|eot)(\?|$)' params_clean.txt > params_dyn.txt
```

Extract the unique parameter-name inventory — often more useful than the URLs themselves, because a name like `redirect_url`, `callback`, `file`, `path`, `template`, or `cmd` tells you the bug class before you send a request:

```
unfurl keys < params_dyn.txt | sort -u > param_names.txt
```

### Phase 3 — Classify by bug class with `gf`

`gf` (tomnomnom) applies saved grep patterns to flag URLs whose parameter names match known-dangerous shapes. With the 1ndianl33t/Gf-Patterns or community pattern packs installed:

```
gf xss     < params_dyn.txt | anew xss_candidates.txt
gf ssrf    < params_dyn.txt | anew ssrf_candidates.txt
gf redirect< params_dyn.txt | anew redirect_candidates.txt
gf lfi     < params_dyn.txt | anew lfi_candidates.txt
gf sqli    < params_dyn.txt | anew sqli_candidates.txt
```

`anew` appends only lines not already present, so re-running paramspider later and re-classifying grows these candidate files monotonically without duplicates — the foundation of the diffing workflow in Phase 5.

### Phase 4 — Validate against the live target (where scope rules apply)

Now, and only now, you touch the program's asset. First confirm which archived URLs still resolve, so you are not fuzzing 404s:

```
httpx -silent -mc 200,301,302,401,403 < params_dyn.txt > live_params.txt
```

Then feed each class to its validator. Swap the `FUZZ` placeholder for a real payload with `qsreplace`, and throttle every active tool:

```
# reflected-XSS triage on the classified candidates (low concurrency)
dalfox file xss_candidates.txt --skip-bav --worker 10 --delay 200

# templated DAST pass with nuclei fuzzing templates (rate-capped)
nuclei -l live_params.txt -dast -rl 20 -c 10

# manual reflection probe: inject a marker, grep it back
qsreplace 'canary7h3r3' < xss_candidates.txt \
  | httpx -silent -mr 'canary7h3r3' > reflected.txt
```

`--delay`/`-rl` here are the program-safety controls: paramspider itself sent nothing to the target, but these downstream tools send one request per parameter per payload and will flood an endpoint if uncapped.

### Phase 5 — Diff runs for newly-shipped parameters

The archive keeps accreting URLs as the Wayback crawler revisits the target, so a paramspider run a month later surfaces parameters that did not exist before — the freshest, least-reviewed attack surface. But **ParamSpider overwrites `results/<domain>.txt` on every run**, so you must snapshot yourself:

```
# snapshot this run under a timestamp, never let it clobber the baseline
cp results/example.com.txt "snapshots/example.com.$(date +%F).txt"

# new parameterized URLs since the last baseline
comm -13 <(sort snapshots/example.com.2026-10-15.txt) \
         <(sort snapshots/example.com.2026-11-16.txt) > new_params.txt

# or, param-name level: which parameter names are new?
comm -13 <(unfurl keys < snapshots/example.com.2026-10-15.txt | sort -u) \
         <(unfurl keys < snapshots/example.com.2026-11-16.txt | sort -u)
```

A newly-appeared `?debug=`, `?impersonate=`, `?preview_token=`, or `?next=` is worth a manual look the day you see it. Running this diff on a schedule (cron the paramspider+snapshot+comm chain) turns a one-shot discovery tool into a continuous new-surface monitor.

---

## Where the money is with paramspider

ParamSpider never produces a finding by itself — it produces *candidates*. The payouts come from the bug classes its parameter-name inventory points you at. This table maps the parameter shapes it surfaces to the class, the validator that confirms it, and how likely the result is already a duplicate.

| Finding class | What paramspider contributes | Validator | Typical severity | Dupe risk |
|---|---|---|---|---|
| Reflected XSS | Params historically carrying reflected values (`q`, `search`, `redirect`, `msg`, `error`) | `dalfox file`, reflection probe | Medium–High | **High** — archive-sourced reflected params are everyone's first scan |
| Open redirect | `next`, `url`, `return`, `redirect`, `callback`, `dest` names | `dalfox`, manual `?next=//evil` | Low–Medium | High |
| SSRF | `url`, `uri`, `path`, `dest`, `feed`, `webhook`, `callback`, `proxy` | nuclei `-dast`, interactsh canary | High–Critical | Medium — fewer hunters chase param-based SSRF to completion |
| LFI / path traversal | `file`, `path`, `template`, `page`, `doc`, `download`, `lang` | nuclei LFI templates, `../` probes | High | Medium |
| SQL injection | `id`, `user`, `category`, `sort`, `order`, `filter` | `sqlmap -m`, nuclei sqli | High–Critical | Medium |
| Deprecated/forgotten endpoints | Archived paths that 404 on a live crawl but still route | `httpx` liveness + manual | Varies — often the best payouts | **Low** — nobody else crawled the dead link |
| Hidden parameters on live routes | Param names seen historically but absent from current forms | `arjun` seeded with `param_names.txt` | Medium–High | Low–Medium |

The low-dupe-risk rows are where paramspider earns its place: **forgotten endpoints** (an old `/beta/`, `/v1/`, or `/internal/` path the live site no longer links but the server still answers) and **parameters dropped from the UI but still honored by the backend**. A live crawler cannot find either, because the links are gone. That is the archive-sourcing advantage made concrete — and it is the one angle where you are not racing fifty other hunters running the same `nuclei -dast` over the same reflected `q=` parameter.

Feed the parameter-name inventory into a dedicated hidden-parameter tool for the second low-dupe row:

```
arjun -u https://example.com/endpoint -w param_names.txt
```

---

## Command recipes

Annotated end-to-end blocks. Every flag shown is one of the five that exist. The low-volume settings live in the downstream tools, not in paramspider (which is already zero-traffic against the target).

### Single-domain harvest → classified candidates

```
paramspider -d example.com
# [INFO] Saved cleaned URLs to results/example.com.txt
uro -i results/example.com.txt | gf xss | anew xss_candidates.txt
# Expected: a deduped candidate file; count it before scanning
wc -l xss_candidates.txt
```

### Scope-disciplined list run (in-scope subdomains only)

```
# build a list, filter to scope, THEN harvest
subfinder -d example.com -silent | grep -vE '(internal|staging)\.example\.com' > subs.txt
paramspider -l subs.txt
# each host -> results/<host>.txt; concatenate for a single surface
cat results/*.example.com.txt | uro > all_params.txt
```

### Stream mode for quick eyeballing

```
paramspider -d example.com -s
# URLs print to the terminal as found (the same filtered set that hits the file);
# pipe straight into a classifier without waiting for the run to finish:
paramspider -d example.com -s | gf ssrf
```

### Preload a reflection probe via the placeholder

```
paramspider -d example.com -p '"><h1>cr7</h1>'
# every parameter value becomes the probe string; feed to httpx and grep the marker back
httpx -silent -mr '<h1>cr7</h1>' < results/example.com.txt > reflected.txt
```

Note the double-edged nature: a `-p` payload containing characters that break URL parsing (unencoded `<`, `>`, spaces, quotes) can make some downstream tools choke. Prefer the default `FUZZ` for the harvest, then substitute payloads per-tool with `qsreplace`, which URL-encodes correctly.

### Route the archive lookup through Burp (debugging/auditing the CDX query)

```
paramspider -d example.com --proxy 127.0.0.1:8080
# sends the web.archive.org CDX request through your proxy — useful to inspect/replay
# the exact CDX query, NOT to proxy traffic to the target (there is none)
```

### Full passive→active chain, rate-capped

```
paramspider -d example.com
uro -i results/example.com.txt \
  | grep -ivE '\.(ico|map|avif)(\?|$)' \
  | httpx -silent -mc 200,301,302,401,403 \
  | tee live.txt \
  | gf xss | anew xss_live.txt
dalfox file xss_live.txt --skip-bav --worker 10 --delay 250   # <-- throttle lives here
nuclei -l live.txt -dast -rl 20 -c 10                          # <-- and here
```

---

## Pitfalls, noise and scope discipline

**Documenting flags that no longer exist.** The single biggest source of wasted time with this tool is copying commands from pre-2024 blog posts. The current 1.0.x rewrite removed `-e`/`--exclude`, `-o`/`--output`, `-s`/`--subs` (the short `-s` now means `--stream`), `-r`/recursion, `-q`/quiet, and `--level`. There is **no flag to add or remove excluded extensions** — the 17-entry list is hardcoded in source. There is **no output-path flag** — results always go to `./results/<domain>.txt`. A proposed `-dp/--disable-placeholder` (PR #118) is **not merged into any release**; do not rely on it. If a command errors with `unrecognized arguments`, you are reading documentation for a dead version.

**Overwrite-on-rerun silently destroys your baseline.** Each run opens `results/<domain>.txt` in write mode. Run paramspider twice and the first result is gone. Any diffing or trend-monitoring workflow (Phase 5) **must** copy the output to a timestamped snapshot immediately after each run, or you lose the ability to tell new parameters from old.

**Archive staleness = a surface full of 404s.** Wayback data is historical. A large fraction of harvested URLs no longer resolve, and feeding the raw file straight into a fuzzer wastes the fuzzer's budget (and your rate allowance) on dead endpoints. Always gate with an `httpx` liveness pass before any active scan. Conversely, do not *discard* the 404s blindly — an endpoint that 404s via the front door but still routes internally is exactly the forgotten-endpoint payout; probe a sample manually.

**It is zero-traffic to the target — but your next tool is not.** ParamSpider's safety story (no packets to the program's asset) evaporates the moment you pipe its output into dalfox, nuclei, sqlmap, or arjun. Those send one-or-many requests *per parameter*, and an uncapped run over a multi-thousand-line paramspider file is exactly the kind of volume that gets hunters throttled, blocked, or reported for DoS. The rate caps (`--delay`, `-rl`, `--worker`, `-c`) belong on every active stage. Never parallelize a fuzzer across the full raw file without a liveness filter and a concurrency cap first.

**Scope creep through over-broad lists.** `-l` runs every host in the file. If subdomain enumeration swept in assets outside the program's scope, paramspider will happily mine them and hand you out-of-scope URLs that are easy to fuzz by accident. Filter the host list to confirmed in-scope assets *before* paramspider, and re-check the output host-by-host before any active tool.

**The placeholder is not a payload.** `FUZZ` (or a `-p` string) is a literal stand-in for the parameter value, not an injection. Tools that treat the whole URL as-is will send the literal `FUZZ`. You must substitute real payloads downstream (`qsreplace`) — paramspider only shapes the URL list, it does not test anything.

**Dependence on Wayback availability.** The tool has a single data source. If `web.archive.org`'s CDX endpoint is rate-limiting, down, or returns an empty set for a thinly-archived target, paramspider produces little or nothing — that is a property of the archive, not evidence the target has no parameters. Corroborate with a live crawler (katana, gau, waymore) for coverage; paramspider is one archive view, not the whole picture.

---

## Reporting evidence

ParamSpider is almost never the subject of a report — the downstream validator is. But its output is the **provenance trail** that makes a "how did you find this?" answer credible, and triagers increasingly ask. Capture:

- **The discovery command and its output line.** `paramspider -d example.com` → the `[INFO] Saved cleaned URLs to results/example.com.txt` line and the specific URL(s) that led to the finding. This shows the parameter was publicly archived, not guessed.
- **The archive citation.** For a forgotten-endpoint or dropped-parameter finding, include the Wayback timestamp proving the endpoint/parameter is a real historical artifact of the target — `https://web.archive.org/web/*/https://example.com/old/endpoint?param=*`. This pre-empts "that parameter doesn't exist" rejections.
- **The liveness proof.** The `httpx` line showing the archived URL still returns a non-404 today — the bridge from "historically seen" to "currently exploitable."
- **The validated PoC, not the candidate.** The actual request/response pair from the downstream tool (dalfox reflection, nuclei match, manual `curl`) with the injected marker/payload visible in the response. A `gf xss` hit alone is not a finding; the reflected-and-executed marker is.
- **Scope confirmation.** For list-based runs, note that the affected host was within the program's declared scope — useful when a finding sits on a subdomain the triager has to look up.

Keep the chain tight: *archived URL (paramspider) → still live (httpx) → exploitable (validator)*. Each link is one artifact; together they are an airtight, reproducible report that survives triage. Never include the raw multi-thousand-line paramspider dump in a report — cite only the specific URL(s) the finding rode in on.

---

## References

- ParamSpider repository (devanshbatham/ParamSpider), README and `paramspider/main.py` source — https://github.com/devanshbatham/ParamSpider
- `setup.py` (console-script entry point, dependencies) — https://github.com/devanshbatham/ParamSpider/blob/master/setup.py
- PR #118 "Add -dp / --disable-placeholder flag" (unmerged) — https://github.com/devanshbatham/ParamSpider/pull/118
- Debian package `paramspider` 1.0.1 man page and README — https://sources.debian.org/src/paramspider/1.0.1-3/README.md and https://dyn.manpages.debian.org/trixie/paramspider/paramspider.1
- Wayback Machine CDX Server API (the sole data source) — https://github.com/internetarchive/wayback/tree/master/wayback-cdx-server
- gf (tomnomnom) — https://github.com/tomnomnom/gf ; Gf-Patterns (1ndianl33t) — https://github.com/1ndianl33t/Gf-Patterns
- qsreplace, anew, unfurl (tomnomnom) — https://github.com/tomnomnom/qsreplace , https://github.com/tomnomnom/anew , https://github.com/tomnomnom/unfurl
- uro (s0md3v) — https://github.com/s0md3v/uro ; arjun (s0md3v) — https://github.com/s0md3v/arjun
- dalfox — https://github.com/hahwul/dalfox ; nuclei DAST/fuzzing — https://docs.projectdiscovery.io/tools/nuclei/fuzzing ; httpx — https://github.com/projectdiscovery/httpx
- YesWeHack, "Parameter Discovery: a quick guide to start" — https://www.yeswehack.com/learn-bug-bounty/parameter-discovery-quick-guide-to-start

*Research supplement added 2026-11-16, grounded in the ParamSpider GitHub source (README + `paramspider/main.py` argparse, confirming the five-flag 1.0.x surface and hardcoded extension list), the Debian 1.0.1 package man page, PR #118, and the downstream tomnomnom/s0md3v/ProjectDiscovery tooling docs cited above (model: claude-opus-4-8).*
