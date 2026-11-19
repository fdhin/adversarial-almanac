---
name: "httpx"
categories:
  - blackarch-webapp
version: "v1.11.0.r40.gd3f78b1"
homepage: "https://github.com/projectdiscovery/httpx"
packaged-by: [blackarch, arch, aur, nixpkgs]
blackarch-builddate: "2026-10-31"
arch-builddate: "2025-12-16"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "projectdiscovery/httpx"
github-stars: 10592
github-pushed: "2026-11-06"
github-archived: false
github-issues: 8
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 358
gh-activity-checked: "2026-11-09"
---

# httpx

> Fast multi-purpose HTTP probing toolkit: feed it hosts or URLs and it reports which are live and what each is running.

## What it's for

httpx (ProjectDiscovery) is the standard recon-stage prober: it takes hosts, domains and URLs from arguments, a file or stdin, and answers for each whether it speaks HTTP(S) — plus status code, page title, content length, web server, technology stack, IP, CDN/WAF presence and more, concurrently (retryablehttp under the hood). Reach for it between subdomain enumeration and deep scanning to separate live web assets from dead ones and fingerprint them in a single pass. It probes and reports; it does not crawl or exploit anything. Not to be confused with the unrelated Ruby HTTP client library of the same name on RubyGems.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Triage a host list into live, annotated web assets

Separate reachable web services from dead hostnames in one pass.

```
httpx -l hosts.txt -sc -title -server -td -ip
```

Outcome: prints one line per responding host with URL, status code, page title, Server header, detected technologies and resolved IP; unreachable hosts are silently dropped. HTTPS is probed first with automatic fallback to HTTP.

### Find hosts serving a specific path or response

Narrow thousands of assets to the interesting few with matchers and filters.

```
cat hosts.txt | httpx -path /admin -mc 200,401,403 -fr -title
```

Outcome: probes every host at `/admin`, follows redirects, and prints only hosts whose response status is 200, 401 or 403 — the inverse (`-fc 404,403`) keeps everything except the given codes.

### Check for well-known files with content validation

Probe standard paths and match on response semantics, not just status.

```
echo target.example.com | httpx -path '/.well-known/security.txt,/security.txt' -mc 200 -mdc 'contains(content_type, "text/plain") && contains(body, "Contact:")'
```

Outcome: prints hosts that expose a valid RFC 9116 security.txt at the standard paths; the `-mdc` DSL requires the right content type and a `Contact:` line in the body, eliminating false positives from generic 404-soft pages.

### Persist results for downstream tooling

Capture structured output and raw responses while scanning.

```
httpx -l hosts.txt -j -o results.jsonl -sr -srd responses/
```

Outcome: every probed result is written as a JSON line to `results.jsonl` and each raw HTTP response is stored under `responses/` for later inspection or diffing.

---

## Bug bounty workflow with httpx

httpx is not an exploitation tool — it is the **validation layer between enumeration and attack**. Subdomain enumeration (subfinder, amass, chaos) and port discovery (naabu) produce thousands of candidate hosts, most of them dead, parked, or out of scope. nuclei, ffuf, and your manual testing are expensive to point at that raw list. httpx sits in the middle: it answers "which of these actually speak HTTP, what is each one running, and which handful deserve a human's attention," then hands a clean, annotated, de-duplicated target list downstream. Get this phase right and every later phase is faster and quieter.

Everything below assumes an **authorized program**: you have the scope in writing, you honor the program's rate and user-agent rules, and you never probe hosts that resolve into infrastructure the program did not list. All example targets are `example.com`, `*.example.com`, or the `198.51.100.0/24` TEST-NET range.

### Phase 0 — Scope intake and safe defaults

Before the first probe, pin the settings that keep you inside the rules of engagement. httpx's defaults (150 req/s, 50 threads, a rotating random User-Agent, an automatic update check, and — on some builds — cloud upload hooks) are tuned for speed on your own assets, not for a third party's production estate.

```bash
# A reusable low-volume, in-scope baseline. Everything after this inherits it.
httpx -l scope_hosts.txt \
  -rl 20 -t 20 -timeout 8 -retries 1 \
  -H 'User-Agent: bugbounty-yourhandle (hackerone.com/yourhandle)' \
  -e private-ips -deny 10.0.0.0/8,172.16.0.0/12,192.168.0.0/16,169.254.169.254/32 \
  -duc -silent -sc -title
```

- `-rl 20` caps requests per second (default is **150**); `-t 20` caps concurrency (default **50**). On a large asset list these two defaults alone can look like an attack. `-rlm` caps per-minute instead if the program specifies a minute budget. `-delay 200ms` adds spacing on top.
- `-H 'User-Agent: ...'` overrides the rotating agent. **httpx sets `-random-agent` to `true` by default**, which defeats programs that require an identifying UA — set yours explicitly and match the program's required format.
- `-e private-ips` and an explicit `-deny` list stop httpx from probing any scope host whose DNS resolves to RFC1918 space or the cloud metadata address (`169.254.169.254`). A dangling or attacker-influenced DNS record pointing inward is exactly how an innocent recon run becomes an out-of-scope SSRF-adjacent incident. `-allow` is the inverse allowlist.
- `-duc` disables the automatic update check (one less outbound call you did not intend).
- **Cloud-upload caution:** httpx can push results to the ProjectDiscovery Cloud Platform via `-pd`/`-dashboard` or the `ENABLE_CLOUD_UPLOAD` environment variable. Uploading a client's live-host inventory to a third-party SaaS can breach program confidentiality or an NDA. Leave these **off** for bounty work unless the program explicitly permits it.

### Phase 1 — First-pass liveness triage and fingerprint

One pass turns the enumeration dump into an annotated live-asset table. This is the single most-used httpx invocation in bug bounty.

```bash
subfinder -d example.com -silent \
  | httpx -rl 20 -t 20 \
      -sc -title -td -server -cdn -ip -cname -location -method \
      -fr -json -o live.jsonl
```

- `-sc` status code, `-title` page title, `-td` technology detection (Wappalyzer dataset), `-server`/`-web-server` the `Server` header, `-cdn` CDN/WAF detection (**on by default**), `-ip`/`-cname` resolution, `-location` the redirect target, `-method` the method used.
- `-fr` follows redirects so a host that 301s to its real app is classified by the destination, not the hop. `-fhr` restricts following to the same host (avoids chasing a redirect off-scope); `-maxr` caps the redirect chain (default **10**).
- `-json` (`-j`) emits one JSON object per host — the structured form nuclei and your own `jq` filters consume. httpx probes **HTTPS first and falls back to HTTP** only if HTTPS is unreachable; add `-nf`/`-no-fallback` to record both schemes, or `-nfs` to probe only the scheme you fed in.

The JSON (`live.jsonl`) carries far more than the console line — `tech`, `webserver`, `cname`, `cdn_name`, `jarm`, `hash`, `tls` — and is what every later phase filters against. Keep it.

### Phase 2 — Non-standard ports and multi-IP hosts

Enumeration tools find names; interesting services hide on non-standard ports behind those names. Pair naabu (or a cautious httpx port sweep) with httpx classification.

```bash
# naabu finds open ports; httpx classifies each host:port. (naabu does the scanning;
# let httpx probe only the ports naabu confirmed open to stay low-volume.)
naabu -l scope_hosts.txt -silent -rate 100 \
  | httpx -rl 20 -t 20 -sc -title -td -server -json -o live_ports.jsonl

# Or let httpx probe a named set of ports directly (nmap syntax, scheme-aware):
httpx -l scope_hosts.txt -p http:80,http:8080,https:443,https:8443,8000,8888,9000 \
      -rl 15 -sc -title -server -json -o altports.jsonl
```

- `-p`/`-ports` takes nmap-style syntax and can bind a scheme to a port (`http:8080`, `https:8443`). A **broad** `-p` range turns httpx into a port scanner — that is often noisy, slow, and outside a web-app program's scope, so confirm ports with a dedicated scanner first and feed httpx the open ones.
- `-pa`/`-probe-all-ips` probes every IP a hostname resolves to (useful behind round-robin DNS or multi-region deployments where one IP is misconfigured and the others are not), at the cost of more requests per host.
- `-ec`/`-exclude-cdn` restricts hosts fronted by a CDN/WAF to ports 80/443 only, so you do not waste a port sweep against an edge that answers everything identically.

### Phase 3 — Surface classification: isolate the juicy few

Now filter the annotated list down to what a human should look at. **Matchers keep** results that match; **filters drop** results that match — the distinction is the whole game here.

```bash
# Auth surfaces: probe a path list, keep auth-relevant codes, follow redirects
httpx -l live_hosts.txt \
  -path '/admin,/login,/dashboard,/api,/actuator,/.git/config,/.env,/server-status' \
  -mc 200,401,403 -fr -sc -title -td -json -o surfaces.jsonl

# Login/console panels by title or body, dropping parked/error/captcha noise
httpx -l live_hosts.txt -ms 'login,sign in,password,dashboard' \
      -fpt login,captcha,parked -sc -title -o panels.txt

# Content-aware match with the DSL — a real exposed .git, not a soft 404
httpx -l live_hosts.txt -path '/.git/config' \
      -mdc 'status_code == 200 && contains(body, "[core]")' -sc -title
```

- `-mc`/`-match-code` keeps only the given status codes (`-mc 200,401,403`); `-fc`/`-filter-code` drops them (`-fc 404,429`). Note the flag is `-match-code`, not "match-status-code."
- `-ms`/`-match-string` and `-mr`/`-match-regex` keep hosts whose response contains a string/regex; `-fs`/`-fe` are the drop equivalents. These are your login-panel and error-signature detectors.
- `-fpt`/`-filter-page-type` drops ML-classified `login`, `captcha`, and `parked` pages — the modern replacement for the deprecated `-fep`/`-filter-error-page`. Used as a **filter** it removes noise; combine with `-ms` to isolate real panels from parked junk.
- `-mdc`/`-match-condition` (and `-fdc` to filter) evaluate a DSL expression over response fields — `status_code`, `title`, `body`, `content_type`, `tech`, `webserver`, `content_length`, and more. **Run `httpx -ldv`/`-list-dsl-variables` to print the exact field names your build exposes** before writing a condition. `contains()`, `contains_any()`, `==`, `&&`, `||` are the common operators. This is how you eliminate the soft-404 false positives that plague raw `-mc 200` path brute-forcing.
- `-fd`/`-filter-duplicates` collapses near-identical responses (a wildcard vhost answering every name the same way) down to one representative — essential before handing a list to nuclei.

### Phase 4 — Pivots that expand the attack surface

httpx's fingerprint fields are pivots, not just labels. Each can widen scope *within* the program.

```bash
# Favicon hash: fingerprint the app, then find every host running the same one
httpx -l live_hosts.txt -favicon -json -o fav.jsonl
#   -> pull mmh3 hashes; a known hash (Jenkins, GitLab, Grafana, Spring Boot) tells
#      you the app before you open it. -mfc keeps only hosts matching a target hash:
httpx -l live_hosts.txt -mfc -1234567890 -title -sc

# CNAME + title: dangling-CNAME subdomain-takeover candidates
httpx -l live_hosts.txt -cname -sc -title -mc 404 -json -o cname404.jsonl
#   -> a CNAME to an unclaimed SaaS (github.io, s3, herokudns, azurewebsites) returning
#      a service "not found" page is a takeover candidate -> hand to nuclei takeover
#      templates or subjack to confirm; httpx surfaces, it does not claim.

# TLS SAN / CSP pivots: new in-scope names hiding in certificates and headers
httpx -l live_hosts.txt -tls-grab -tls-probe -csp-probe -json -o tls.jsonl
#   -> -tls-probe re-probes DNS names found in the certificate SAN; -csp-probe
#      re-probes domains named in Content-Security-Policy. Validate each is in scope.

# ASN / JARM for clustering infrastructure
echo AS64496 | httpx -silent -sc -title        # probe an org's whole ASN (if in scope)
httpx -l live_hosts.txt -jarm -json -o jarm.jsonl   # cluster by TLS stack fingerprint
```

- `-favicon` computes the mmh3 hash of `/favicon.ico`; `-mfc`/`-ffc` match/filter on it. Favicon hashes are stable app fingerprints — the same pivot Shodan's `http.favicon.hash` uses.
- `-hash md5|mmh3|sha256|...` fingerprints the body for grouping identical deployments.
- `-tls-grab`, `-tls-probe`, and `-csp-probe` turn one host's certificate and headers into more in-scope hostnames. **Re-check scope on every discovered name** before probing it further — a SAN can list domains the program does not own.
- `-asn`/`-cname`/`-ip` and `-jarm` cluster hosts by owner and stack, which is how you spot the one unmanaged box in an otherwise uniform fleet.

### Phase 5 — Handoff to nuclei and screenshotting

httpx's output is the input contract for the next stage. Keep the formats aligned.

```bash
# Clean URL list for nuclei (strip everything but the probed URL)
httpx -l live_hosts.txt -rl 20 -silent -o nuclei_targets.txt
nuclei -l nuclei_targets.txt -rl 30 -c 20 -o nuclei_out.txt

# Richer handoff: filter the JSON, then feed only the live 200s running a known stack
jq -r 'select(.status_code==200 and (.tech // [] | index("WordPress"))) | .url' \
  live.jsonl > wordpress_targets.txt
nuclei -l wordpress_targets.txt -tags wordpress -rl 30

# Visual triage: screenshot the shortlist (headless Chrome; slower, heavier)
httpx -l panels.txt -ss -system-chrome -srd shots/ -json -o shots.jsonl
```

- Feeding nuclei the **httpx-validated** list (not the raw enumeration dump) is the whole point: nuclei then only fires templates at hosts confirmed live, de-duplicated, and optionally pre-filtered by tech — far fewer requests against the target.
- `-ss`/`-screenshot` drives a headless browser (go-rod auto-manages Chrome; `-system-chrome` uses a local install). It is much slower and heavier than a normal probe, so screenshot the **shortlist**, never the whole estate. With `-json` the rendered DOM is included; `-esb`/`-ehb` trim screenshot bytes and headless body from the JSON. gowitness and Aquatone are the common standalone alternatives if you prefer a dedicated gallery.

### Phase 6 — Continuous monitoring: diff the estate over time

The freshest, least-reviewed attack surface is whatever shipped since the last hunter looked. httpx's JSON output makes a target a tracked object: snapshot it on a schedule and diff the snapshots. A newly-appeared host, a status that flipped from 401 to 200, a new technology on an existing host, or a changed body hash are all signals pointing at just-deployed, under-tested code.

```bash
# Re-run the Phase 1 pass on a cadence, dated, then diff host sets
httpx -l scope_hosts.txt -rl 20 -silent -sc -title -td -hash sha256 \
      -json -o "snap_$(date +%F).jsonl"

# New live URLs since the last snapshot
comm -13 \
  <(jq -r '.url' snap_2026-11-01.jsonl | sort) \
  <(jq -r '.url' snap_2026-11-15.jsonl | sort)

# Hosts whose body hash changed (same URL, new content = redeploy).
# Confirm the hash JSON key name for your build first: httpx -ldv (it is reported
# under the .hash object; adjust the jq path to the key -ldv prints).
join -t$'\t' \
  <(jq -r '[.url,(.hash|tostring)]|@tsv' snap_2026-11-01.jsonl | sort) \
  <(jq -r '[.url,(.hash|tostring)]|@tsv' snap_2026-11-15.jsonl | sort) \
  | awk -F'\t' '$2 != $3'
```

- `-hash sha256` (or `mmh3`/`simhash`) gives each body a stable identity to diff against; `simhash` is tolerant of trivial per-request noise (CSRF tokens, timestamps) when exact hashes churn too much.
- Store the dated JSONL and feed only the *delta* to Phase 3–5, so every monitoring run costs a full triage pass once and cheap targeted follow-up thereafter. `-resume` lets a long interrupted scan continue from `resume.cfg` rather than restart.

### Phase 7 — HTTP methods and virtual hosts

Two surfaces the basic triage pass skips, each its own finding class.

```bash
# Which methods does each host actually answer? (WebDAV PUT, unexpected DELETE/TRACE)
httpx -l live_hosts.txt -rl 15 -x all -method -sc -json -o methods.jsonl
jq -r 'select(.method=="PUT" and .status_code<300) | .url' methods.jsonl
#   -> -x all probes every method and -method records which one was used; a 200/201 on
#      PUT is a potential arbitrary-file-write (test writing to a path you own, then
#      delete it). TRACE enabled is a lower-severity XST note.

# Virtual-host discovery: names that only resolve inside the Host header
httpx -u 198.51.100.10 -vhost-input vhost_candidates.txt -rl 15 -sc -title -fd
#   -> probes each candidate name against the one IP via the Host header; -fd drops the
#      wildcard/default response so only vhosts that differ from the default surface.
#      Internal apps bound to a vhost but not DNS-published show up here.
```

- `-x` with `all` probes every method (`-x PUT,DELETE` for a specific set); a state-changing method that returns success is a report — demonstrate it against an object you own and clean up.
- `-vhost` checks whether a host supports virtual hosting; `-vhost-input` takes a candidate-name list and probes them against a target IP. `-rr`/`-request` lets you feed a full raw request file for cases the flags cannot express.

---

## Where the money is with httpx

httpx rarely *is* the finding — it is what puts you one step from it by surfacing the asset and its fingerprint. Priority is by how reliably the httpx feature isolates a payable surface versus how often every other hunter's httpx run already reported it.

| Finding class httpx leads to | httpx feature / flags that reach it | Typical severity | Dupe risk |
|---|---|---|---|
| Subdomain takeover (dangling CNAME to unclaimed service) | `-cname` + `-mc 404` + `-title`/`-mdc` on the service's "not found" body | High | Medium |
| Forgotten service on a non-standard port | `-p`/`-ports` (scheme-aware) + `-sc -title -server` | Medium–High | Low–Medium |
| Exposed `.git`/`.env`/`.svn`/backup files | `-path` + `-mdc 'status_code==200 && contains(body,"...")'` | Medium–High | High (`.git` especially) |
| Unauthenticated admin/console panel | `-path` + `-mc 200` + `-ms`/`-fpt` to confirm it is a real login/console | Medium (High with default creds) | High |
| Outdated/vulnerable component (feeds nuclei/CVE match) | `-td` + `-server` + `-favicon` to pin app and version | Varies (follows the CVE) | Medium |
| Exposed debug/status endpoints (actuator, phpinfo, swagger) | `-path` + `-ms`/`-mdc` on the body signature | Medium | High |
| Virtual-host–only internal app | `-vhost` / `-vhost-input` | Medium–High | Low |
| Favicon-hash pivot to a known vulnerable app | `-favicon` + `-mfc` on the target hash | Varies | Medium |
| Plaintext HTTP where HTTPS is expected | `-nf`/`-no-fallback` (records both schemes) + `-sc` | Low–Medium | Medium |
| Missing/weak security headers | `-irh` in `-json` (headers in output) | Low/Info | Very high |

The top three rows are where httpx-led reports actually pay: takeover candidates, services on odd ports, and exposed source/secrets. The bottom rows are real but near-universally duplicated — report them as supporting context, not as the headline, unless you can chain them into impact.

---

## Command recipes

Annotated, copy-pasteable, with the low-volume settings called out. Every target is `example.com` or TEST-NET.

```bash
# 1. The everyday triage pass (low and polite)
subfinder -d example.com -silent \
  | httpx -rl 20 -t 20 -timeout 8 \
      -sc -title -td -server -cdn -ip -fr -json -o live.jsonl
# Expected: one JSON line per live host with status_code, title, tech[], webserver,
# a[] (IPs), cdn_name. Dead hosts are dropped silently. ~1 request per host (plus
# redirects). At -rl 20 a 5,000-host list takes a few minutes, not a flood.
```

```bash
# 2. Confirm a real exposed .git (no soft-404 false positives)
httpx -l live_hosts.txt -rl 10 \
  -path '/.git/config,/.git/HEAD' \
  -mdc 'status_code == 200 && contains(body, "[core]")' \
  -sc -title -srd evidence/ -sr
# Expected: only hosts serving a genuine git config print. -sr/-srd save the raw
# response under evidence/ as proof. contains(body,"[core]") is what separates a
# live repo from a 200-returning catch-all. Run -ldv first to confirm DSL field names.
```

```bash
# 3. Subdomain-takeover candidate sweep
httpx -l live_hosts.txt -rl 15 -cname -sc -title -json -o cname.jsonl
jq -r 'select(.status_code==404) | "\(.input)\t\(.cname // [] | join(","))\t\(.title)"' cname.jsonl
# Expected: hostname, its CNAME target, and the 404 title. A CNAME to an unclaimed
# *.github.io / *.s3.amazonaws.com / *.herokudns.com / *.azurewebsites.net with the
# provider's "no such app" page is the candidate. Confirm with a takeover template in
# nuclei before claiming — httpx surfaces, it does not prove ownership.
```

```bash
# 4. Non-standard-port classification from a confirmed-open port list
naabu -l scope.txt -silent -rate 100 -o open_ports.txt
httpx -l open_ports.txt -rl 15 -sc -title -td -server -json -o ports.jsonl
# Expected: each host:port classified. Let naabu do the scanning at its own rate;
# httpx only probes ports already confirmed open, so you add web classification
# without a second port sweep. Broad httpx -p ranges would double the noise.
```

```bash
# 5. Favicon fingerprinting and pivot
httpx -l live_hosts.txt -rl 20 -favicon -json -o fav.jsonl
jq -r '"\(.favicon)\t\(.url)"' fav.jsonl | sort | uniq -c | sort -rn
# Expected: counts of each mmh3 favicon hash across the estate. A cluster sharing one
# known hash (e.g. a Spring Boot or Grafana default icon) is a batch of same-app hosts
# to test together. -mfc <hash> then keeps only that cluster on the next run.
```

```bash
# 6. Clean handoff to nuclei (the pipeline payoff)
httpx -l live_hosts.txt -rl 20 -silent -fd -o nuclei_targets.txt
nuclei -l nuclei_targets.txt -rl 30 -c 20 -o nuclei_out.txt
# Expected: -fd drops near-duplicate wildcard responses so nuclei is not pointed at
# 500 identical vhosts. nuclei fires only at validated live URLs — the request budget
# stays proportional to real assets, which is what keeps a large program in-scope.
```

```bash
# 7. Screenshot the shortlist for visual triage (heavy — shortlist only)
httpx -l panels.txt -rl 5 -ss -system-chrome -srd shots/ -json -o shots.jsonl
# Expected: a PNG per host under shots/ plus rendered-DOM JSON. Headless Chrome is slow
# and memory-hungry; -rl 5 and a small input list keep it from overwhelming either your
# box or the target. gowitness/Aquatone are standalone alternatives.
```

```bash
# 8. Segment the estate by CDN/WAF before choosing a scan strategy
httpx -l live_hosts.txt -rl 20 -cdn -json -o cdn.jsonl
jq -r 'select(.cdn==true)  | .url' cdn.jsonl > fronted.txt     # behind an edge
jq -r 'select(.cdn!=true)  | .url' cdn.jsonl > origin.txt      # direct-to-origin
# Expected: cdn==true hosts answer uniformly at the edge — port sweeps are wasted
# (use -ec to limit them to 80/443) and loud probing trips the WAF. The origin.txt
# set is where unprotected paths, odd ports, and misconfig actually live; point the
# deeper Phase 3 work there. -mcdn/-fcdn keep or drop specific providers by name.
```

### DSL conditions worth keeping (`-mdc` to match, `-fdc` to filter)

The DSL is what turns `-mc 200` guesswork into precise, low-false-positive hunting. Field names below are the common ones — **always confirm against `httpx -ldv` for your build**, since the set grows between releases.

| Goal | Condition |
|---|---|
| Real exposed `.git` (not a soft-404) | `status_code==200 && contains(body,"[core]")` |
| Exposed env file | `status_code==200 && contains_any(body,"APP_KEY","SECRET","DB_PASSWORD")` |
| Spring Boot actuator | `status_code==200 && contains(body,"\"status\":\"UP\"")` |
| Swagger/OpenAPI surface | `contains(body,"swagger") || contains(body,"openapi")` |
| Login/console by title | `contains(title,"login") || contains(title,"sign in")` |
| Interesting non-HTML body | `contains(content_type,"json") && status_code==200` |
| Big response behind auth | `status_code==200 && content_length>100000` |
| Server banner leak | `contains(webserver,"Apache/2.2") \|\| contains(webserver,"nginx/1.1")` |

Pair any of these with `-sr`/`-srd` so the matching raw response is saved as evidence in the same run.

---

## Pitfalls, noise and scope discipline

How hunters get httpx reports rejected — or get removed from a program:

- **Running the defaults at scale.** 150 req/s × 50 threads against a production estate is indistinguishable from a stress test. Always set `-rl`/`-t` down for third-party targets; add `-delay` and `-rlm` if the program specifies a budget. Screenshots (`-ss`), `-pa` (all IPs), and `-vhost` each multiply request volume — apply them to shortlists, not the full list.
- **Broad `-p`/`-ports` ranges on a web-app program.** A wide port sweep is port scanning, which many web-focused programs explicitly exclude. Confirm open ports with a scanner that is in scope, then feed httpx only those ports.
- **Probing resolved-but-out-of-scope IPs.** A scope *hostname* can resolve to shared infrastructure, a CDN origin you do not own, or internal space. Use `-e private-ips`, an explicit `-deny` for RFC1918 and `169.254.169.254`, and `-allow` to pin the ranges you may touch. This is how you avoid accidentally probing a neighbor on the same CDN or a cloud metadata endpoint.
- **Leaking the target into third-party clouds.** `-pd`/`-dashboard` and `ENABLE_CLOUD_UPLOAD` push results to the ProjectDiscovery Cloud Platform. For bounty work that can violate confidentiality — keep them off unless the program allows it. Likewise `-duc` to silence the update check if you want zero unexpected outbound calls.
- **The rotating User-Agent.** `-random-agent` is **on by default**; programs that require an identifying UA will see random browsers and may flag the traffic. Set `-H 'User-Agent: ...'` to the required format.
- **Reporting soft-404s as findings.** A server that returns 200 for every path makes naive `-mc 200` path brute-forcing produce garbage. Use `-mdc`/`-fdc` content conditions, `-fpt` to drop parked/error pages, and `-fd` to collapse duplicates before you believe a "hit."
- **`-unsafe` and malformed requests.** `-unsafe` sends raw, un-normalized requests — useful for some edge cases but capable of behavior a program may treat as attack traffic. Know why you are using it before you do.
- **Fingerprint ≠ vulnerability.** `-td`/`-server`/`-favicon` tell you *what* is running, not that it is exploitable. Version-based reports with no demonstrated impact are routinely closed as informational — carry the fingerprint into nuclei/manual testing and report the confirmed issue, not the banner.
- **Trusting `-cdn`/WAF labels blindly.** `-cdn` detection (on by default) tells you an edge is in front; it does not mean every path is protected. But it *should* change your strategy — segment CDN-fronted hosts (`-ec` limits them to 80/443) from origin-direct hosts, which are where unprotected surfaces live.

---

## Reporting evidence

httpx is a recon tool, so its output is corroborating evidence, not the finding itself. Capture:

- **The exact command**, including the rate/thread/delay settings, so the triager can see your volume was responsible.
- **The JSON line** (`-json`) for the host in question — it timestamps the observation and records `status_code`, `title`, `tech`, `webserver`, `cname`, `cdn_name`, `ip`, `hash`, and `jarm` in one object. Far more defensible than a console screenshot.
- **The raw response** via `-sr`/`-srd` (and `-include-chain`/`-store-chain` for redirect-based findings) — the actual bytes that prove, e.g., a `.git/config` or an exposed `.env`, stored to a directory you attach. Use `-irh`/`-irr`/`-irrb` to fold request/response (optionally base64) into the JSON when you want a single artifact.
- **For takeover candidates:** the `cname` field plus the response body/title showing the unclaimed-service page — then the confirming tool's output (nuclei takeover template or subjack), because httpx only flags the candidate.
- **For a version/CVE chain:** the `-td`/`-server`/`-favicon` fingerprint *and* the downstream proof (nuclei hit or manual PoC). Never report the fingerprint alone.
- **Reproduce minimally:** a single `httpx -u https://host -path '/the/path' -mdc '...' -sr -srd proof/` against the one host is tighter evidence than re-attaching the whole estate scan. Redact anything the proof does not need, and do not exfiltrate more than the single response that demonstrates the issue.

---

## References

- httpx GitHub repository and README (flag reference, probes/matchers/filters, DSL notes) — https://github.com/projectdiscovery/httpx
- httpx raw README (`main` branch, flag usage block) — https://raw.githubusercontent.com/projectdiscovery/httpx/main/README.md
- ProjectDiscovery docs — httpx usage (full flag list by category) — https://docs.projectdiscovery.io/tools/httpx/usage
- ProjectDiscovery docs — httpx running (example command lines, screenshots, dashboard) — https://docs.projectdiscovery.io/tools/httpx/running
- ProjectDiscovery docs — httpx overview — https://docs.projectdiscovery.io/tools/httpx/overview
- nuclei (downstream scanner the pipeline feeds) — https://github.com/projectdiscovery/nuclei
- subfinder / naabu (upstream enumeration and port discovery) — https://github.com/projectdiscovery/subfinder , https://github.com/projectdiscovery/naabu

*Research supplement added 2026-11-15, grounded in the ProjectDiscovery httpx README and docs.projectdiscovery.io usage/running/overview pages (model: claude-opus-4-8). Flags verified against the `main`-branch README and docs — run `httpx -h` and `httpx -ldv` to confirm names and DSL fields on your installed build.*
