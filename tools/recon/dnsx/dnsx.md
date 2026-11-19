---
name: "dnsx"
categories:
  - blackarch-recon
version: "v1.2.1.r490.g8a9a46b"
homepage: "https://github.com/projectdiscovery/dnsx"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-10-31"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "projectdiscovery/dnsx"
github-stars: 2916
github-pushed: "2026-11-02"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 77
gh-activity-checked: "2026-11-09"
---

# dnsx

> Fast, multi-purpose DNS toolkit from ProjectDiscovery: bulk resolution of any record type, response filtering and enrichment, reverse sweeps, bruteforce and DNS tracing.

## What it's for

dnsx is the standard pipeline tool for resolving large lists of hostnames and IPs. It reads hosts from a file or stdin, queries any record type (A, AAAA, CNAME, NS, TXT, SRV, PTR, MX, SOA, AXFR, CAA, or all at once with `-recon`), and filters or enriches the output on the way out — by DNS status code, with CDN detection, ASN info, custom output templates and JSONL output. It also bruteforces subdomains (`-d` with `-w`), performs PTR sweeps of CIDR blocks and AS numbers, traces delegation paths, and auto-filters wildcard domains. It replaced the discontinued dnsprobe and is designed to sit directly after subdomain enumerators such as subfinder; it does not discover subdomains from passive sources by itself.

## Typical use cases

### Resolve enumerator output silently

```
subfinder -d example.com -silent | dnsx -silent
```

Outcome: prints only the hostnames that resolve, one per line — the usual filter between enumeration and the next pipeline stage.

### Show the actual responses

```
dnsx -l hosts.txt -a -resp -silent
```

Outcome: for each host prints the query name together with the returned A record values; `-resp-only` drops the query name and prints just the answers.

### Filter by DNS status code

```
dnsx -l hosts.txt -silent -rcode noerror
```

Outcome: only hosts whose query completed with the given rcode(s) pass through (`noerror,servfail,refused` and friends), separating dead names from blocked ones.

### Reverse-resolve a netblock

```
echo 198.51.100.0/24 | dnsx -silent -resp-only -ptr
```

Outcome: expands the CIDR, runs a PTR query for every address, and prints the reverse names — hostname discovery straight from IP space; `echo AS17012 | dnsx -silent -resp-only -ptr` does the same for an AS number.

### Bruteforce subdomains against a domain

```
dnsx -silent -d example.com -w words.txt
```

Outcome: resolves `<word>.example.com` for every entry of `words.txt` (an inline comma-separated list works too) and prints the resolving hosts.

### Strip wildcard false positives

```
dnsx -l subdomain_list.txt -auto-wildcard -o clean.txt
```

Outcome: detects wildcard behaviour on the target domain, filters the flood of wildcard answers out, and writes the genuinely resolving hosts to `clean.txt`.

### Trace how a name resolves

```
echo example.com | dnsx -trace
```

Outcome: prints the step-by-step delegation path from the root servers down to the authoritative answer for the name.

---

## Bug bounty workflow with dnsx

dnsx is not a vulnerability scanner, and nothing it prints is a finding on its own. It is the **resolution and enrichment layer** that sits between subdomain enumeration and everything downstream — the stage that decides which of the thousands of candidate names a passive source coughed up are actually real, which are *dangling* (takeover territory), which point at internal infrastructure, and which are CDN noise you should stop port-scanning. Its payouts come from exactly two places:

1. **The misconfigured DNS it surfaces directly** — dangling CNAMEs (subdomain takeover), lame NS delegation, open zone transfer (`-axfr`), and public records that resolve to RFC1918 space (internal-IP disclosure).
2. **The clean, de-noised, enriched host list it hands to the next tool** — a wildcard-filtered, live-only, ASN/CDN-tagged set that stops httpx/nuclei/subjack from drowning in dead names and false positives.

Everything below assumes an authorized program: you resolve only in-scope names, you PTR-sweep only in-scope IP space, you attempt AXFR only against nameservers that are in scope, and you never actually *claim* a third-party resource to "prove" a takeover — you fingerprint it and stop. Treat the resolver as a shared public utility: dnsx ships with no rate limit by default (see Phase 0), and an unthrottled bruteforce is how you get your egress IP blocked and your results silently poisoned.

### Phase 0 — Resolver hygiene and a health baseline

Fix your resolvers *before* you resolve a single target name. dnsx defaults to Google, Cloudflare, and Quad9 — fine for a few hundred names, actively harmful for a large bruteforce. Two failure modes to understand:

- **Rate-induced false negatives.** Push tens of thousands of queries through three public resolvers and they start returning `SERVFAIL`/timeouts. In the output that is indistinguishable from "this name does not exist," so live hosts silently vanish from your list.
- **Resolver pollution → false positives.** A captive-portal resolver, an ISP that NXDOMAIN-hijacks into an ad page, or a resolver that answers everything with a wildcard will hand dnsx *forged* A records. Those become invented subdomains, and an invented subdomain in a report is how you lose triager trust.

Supply a curated, validated resolver list (`-r`) and confirm the toolchain is healthy first:

```
dnsx -hc                                   # diagnostic check: resolver reachability, version, config
dnsx -l hosts.txt -r resolvers.txt -silent # every query goes through your vetted set
```

Build `resolvers.txt` from a list you trust and have validated (e.g. output of a resolver-validator run), not a random "10k free resolvers" gist. dnsx accepts TCP/UDP/DoH/DoT resolver formats in that file. Smaller and trusted beats large and dirty every time.

### Phase 1 — Resolve the enumeration output (live-only, rate-capped)

subfinder/amass/assetfinder produce *candidates*; dnsx keeps only the names that answer. This is the canonical seam:

```
subfinder -silent -d example.com | dnsx -silent -r resolvers.txt -rl 150 -t 50
```

`-rl 150` caps DNS requests/second (the default is `-1`, i.e. **unlimited** — see Pitfalls) and `-t 50` holds concurrency down from the default 100. Add `-a -resp` when you want the A values inline, and keep wildcard filtering on (Phase 1.1) so a wildcard domain does not flood the list with identical answers.

#### 1.1 Kill wildcard answers before they become noise

A domain with a `*.example.com` wildcard answers *every* name you throw at it, so a bruteforce "discovers" thousands of hosts that are all the same record. dnsx has two mutually exclusive controls:

```
dnsx -l candidates.txt -auto-wildcard -r resolvers.txt -o live.txt   # detect wildcard roots automatically
dnsx -l candidates.txt -wd example.com -json -o live.jsonl           # manual: name the wildcard root yourself
```

`-wd`/`-wildcard-domain` is manual wildcard filtering for one named root; the help text warns it is **mutually exclusive with `-auto-wildcard`, other flags are ignored, and JSON output is recommended** — so when you pin a root manually, take `-json` and parse the structured output rather than expecting the usual plain columns. `-wt`/`-wildcard-threshold` (default 5) tunes how many identical answers trip the wildcard heuristic.

### Phase 2 — CNAME extraction → subdomain-takeover candidates

The single highest-value thing dnsx feeds. Pull the CNAME chain for every live host and keep the host↔target pairing intact by taking JSON (so `host`, `cname`, and `status_code` stay on one line):

```
dnsx -l live.txt -silent -cname -json -r resolvers.txt -rl 150 | \
  jq -r 'select(.cname) | "\(.host) -> \(.cname[]) [\(.status_code)]"'
```

The gold is a CNAME pointing at a third-party service whose backing resource no longer exists — `*.s3.amazonaws.com`, `*.github.io`, `*.herokuapp.com`, `*.azurewebsites.net`, `*.cloudfront.net`, `*.pages.dev`, custom-domain SaaS endpoints — where the target was deprovisioned but the DNS record was left behind. Those often resolve to `NXDOMAIN` or `SERVFAIL`, so isolate the dangling ones by status code and hand them to a dedicated takeover detector rather than eyeballing:

```
# dangling-candidate filter: CNAMEs whose lookups returned NXDOMAIN or SERVFAIL
dnsx -l live.txt -silent -cname -resp -r resolvers.txt -rcode nxdomain,servfail

# hand the host list to purpose-built takeover fingerprinting
dnsx -l live.txt -silent -cname -resp-only -r resolvers.txt | sort -u > cnames.txt
subjack  -w live.txt -ssl -v -o takeover.txt
nuclei   -l live.txt -t http/takeovers/ -o nuclei-takeover.txt
```

dnsx finds the *candidate*; subjack's fingerprint database or nuclei's `http/takeovers` templates confirm the service is actually claimable (an unclaimed bucket/page vs. a legitimately-parked CNAME). Report only what the fingerprint confirms — a CNAME to a live, owned third-party endpoint is not a takeover.

### Phase 3 — Reverse sweeps and ASN/CDN enrichment for scoping

For in-scope IP space, PTR sweeps surface hostnames you would never have guessed from a wordlist — the reverse zone is where forgotten `jenkins-old`, `vpn2`, `staging-db` names live. dnsx expands a CIDR or an AS number for you:

```
echo 198.51.100.0/24 | dnsx -silent -ptr -resp-only -r resolvers.txt -rl 100
echo AS64496          | dnsx -silent -ptr -resp-only -r resolvers.txt -rl 100
```

Enrich resolved hosts with CDN and ASN tags so you can separate "target's own origin infrastructure" from "fronted by a CDN you must not port-scan":

```
dnsx -l live.txt -silent -a -asn -cdn -json -r resolvers.txt | \
  jq -r '{host, a, cdn: ."cdn-name", asn: .asn."as-number"}'
```

`-cdn` flags answers that land in known CDN ranges (stop scanning those IPs — they are not the target's host and the program almost certainly forbids it); `-asn` attaches `as-number`/`as-name`/`as-country`/`as-range`, which lets you confirm an IP actually belongs to the target's announced space before you treat it as in-scope.

### Phase 4 — Record-type mining and zone-transfer checks

Beyond A/CNAME, the other record types are reconnaissance and occasionally findings in their own right:

```
dnsx -l live.txt -silent -recon -json -r resolvers.txt -o records.jsonl   # a,aaaa,cname,ns,txt,srv,ptr,mx,soa,axfr,caa in one JSONL pass
```

- **`-txt`** — SPF/DMARC/DKIM and vendor-verification tokens (`google-site-verification`, `atlassian-domain-verification`, etc.) map the target's SaaS footprint; a permissive SPF (`+all`) or missing DMARC is a low-severity email-spoofing note.
- **`-ns`** — nameserver records expose *lame delegation* (a zone delegated to an NS that no longer serves it) and NS-level takeover candidates (delegation to a registerable/claimable provider zone).
- **`-axfr`** — attempt a zone transfer against the target's own nameservers (which must be in scope). A nameserver that answers AXFR for a public zone hands you the entire record set — a clean, well-known misconfiguration:

```
echo example.com | dnsx -silent -axfr -resp -r resolvers.txt
```

### Phase 5 — Internal-IP and misconfiguration triage

The JSONL output flags public names that resolve into RFC1918 / private ranges — a split-horizon DNS leak or a DNS-rebinding primitive, and a reportable information disclosure on its own:

```
dnsx -l live.txt -silent -recon -json -r resolvers.txt | \
  jq -r 'select(.has_internal_ips == true) | "\(.host)  internal=\(.internal_ips)"'
```

`has_internal_ips` / `internal_ips` are emitted by dnsx's underlying resolver library; a corporate hostname answering `10.x`/`172.16.x`/`192.168.x` to the public internet discloses internal addressing and is frequently the first thread of a larger internal-exposure chain.

### Phase 6 — Handoff and change-over-time diffing

The clean list is the deliverable to the rest of the pipeline, and the *diff* is a finding generator on its own — newly appeared subdomains are the freshest, least-reviewed assets on the program:

```
dnsx -l all_candidates.txt -silent -a -r resolvers.txt -o live-$(date +%F).txt
comm -13 live-2026-11-09.txt live-2026-11-16.txt    # names that became live this week
cat live-$(date +%F).txt | httpx -silent | nuclei -silent
```

---

## Where the money is with dnsx

dnsx itself rarely *is* the bounty; it is the instrument that surfaces the DNS-layer finding or feeds the tool that lands it. Priorities below, highest expected value first:

| Priority | Finding class | dnsx feature / command that reaches it | Typical severity | Dupe risk |
|---|---|---|---|---|
| 1 | **Subdomain takeover** (dangling CNAME to unclaimed third-party service) | `-cname -json` + `-rcode nxdomain,servfail` → subjack / `nuclei -t http/takeovers` | High (Critical if auth/cookie-bearing subdomain) | **High** on mature programs — everyone runs the same scan |
| 2 | **NS takeover / lame delegation** (zone delegated to a registerable or dead nameserver) | `-ns -resp`, then test whether the delegated NS is claimable/resolving | High–Critical | Low–Medium |
| 3 | **Open zone transfer (AXFR)** | `-axfr -resp` against each in-scope NS | Medium–High (full-zone disclosure) | Low |
| 4 | **Dangling A/AAAA → cloud IP reuse** (record points at a released cloud IP you can re-allocate) | `-a -resp -asn` to spot abandoned cloud ranges | High | Medium |
| 5 | **Internal-IP disclosure** (public name → RFC1918) | `-recon -json` → `has_internal_ips` / `internal_ips` | Low–Medium | Medium |
| 6 | **Forgotten infrastructure discovery** (feeds other bug classes) | `-ptr` sweep of in-scope CIDR / ASN | Varies (enabler) | Low |
| 7 | **Email-spoofing / vendor disclosure** (SPF `+all`, missing DMARC, verification tokens) | `-txt -resp-only` | Low–Info | High |
| 8 | **Fresh attack surface** (newly added subdomains) | snapshot `-o` + `comm`/diff over time | Enabler / Info | — (your edge is being first) |

The two with the best effort-to-payout ratio are **rows 1 and 6**: takeover is a near-mechanical pipeline (and worth running *continuously*, since records go dangling after the first scan), and PTR sweeps regularly expose an admin panel or staging host no wordlist would have found. Row 1 carries the highest dupe risk precisely because it is mechanical — your differentiator is *continuous* monitoring and acting within hours of a record going dangling, not running the scan once.

---

## Command recipes

Each block pairs a command with what it prints and the low-volume settings that keep you inside program rules. The two knobs that matter most are `-rl` (rate limit — **off by default**) and `-t` (threads — 100 by default); treat both as required, not optional.

### Vet resolvers, then resolve live-only

```
dnsx -hc                                                  # confirm resolvers answer and version is current
subfinder -silent -d example.com | \
  dnsx -silent -r resolvers.txt -rl 150 -t 50 -retry 2
```

Expected: one resolving hostname per line. `-retry 2` (the default) re-asks a failed query once before giving up, which suppresses transient-timeout false negatives without amplifying load. Raise `-rl` only if the program's rules of engagement and your resolver budget both allow it.

### Takeover triage, structured and confirmable

```
dnsx -l live.txt -silent -cname -json -r resolvers.txt -rl 150 | \
  jq -r 'select(.cname) | "\(.host)\t\(.cname[0])\t\(.status_code)"' | \
  tee cname-map.tsv
awk '$3=="NXDOMAIN" || $3=="SERVFAIL"' cname-map.tsv > dangling.tsv
cut -f1 dangling.tsv | nuclei -silent -t http/takeovers/ -o takeover-confirmed.txt
```

Expected: `cname-map.tsv` holds every host→CNAME→status triple; `dangling.tsv` narrows to the records whose lookup failed (the classic dangling signal); nuclei confirms which are *actually* claimable. Only `takeover-confirmed.txt` entries go in a report.

### PTR sweep of an in-scope netblock (rate-capped)

```
echo 198.51.100.0/24 | dnsx -silent -ptr -resp-only -r resolvers.txt -rl 100 -o ptr.txt
```

Expected: reverse names for every address that has a PTR record. `-rl 100` keeps a /24 sweep from spiking your resolver's query rate. For an AS number, swap the input: `echo AS64496 | dnsx -silent -ptr -resp-only ...`.

### Full record inventory as JSONL (one pass, machine-readable)

```
dnsx -l live.txt -silent -recon -json -r resolvers.txt -rl 150 -o records.jsonl
jq -r 'select(.axfr)         | .host'                      records.jsonl   # zones that allowed transfer
jq -r 'select(.has_internal_ips==true) | "\(.host) \(.internal_ips)"' records.jsonl
jq -r 'select(.txt)          | "\(.host) \(.txt|join(" "))"' records.jsonl  # SPF/DMARC/verification tokens
```

`-recon` queries every type (`a,aaaa,cname,ns,txt,srv,ptr,mx,soa,axfr,caa`) in one pass; JSONL keeps each record under its own key (`a`, `cname`, `ns`, `txt`, `mx`, `soa`, `axfr`, `status_code`, `resolver`, `timestamp`, …) so you parse instead of scrape.

### Custom columns for a bespoke pipeline

```
dnsx -l live.txt -silent -a -ot '{{host}} {{a}} {{ttl}}' -r resolvers.txt
```

Expected: `host A-value TTL` per line. The `-ot`/`-output-template` fields include `host`, `a`, `aaaa`, `cname`, `ns`, `txt`, `mx`, `srv`, `ptr`, `soa`, `caa`, `ttl`, `resolver`, `status_code`, `cdn-name`, `cdn-type`, `asn`, `query-time`, plus the `{{ip}}` alias. Note the template flag **cannot be combined with `-json` or `-raw`** — pick columns *or* JSONL, not both.

### Bruteforce a domain (scope- and rate-disciplined)

```
dnsx -silent -d example.com -w words.txt -r resolvers.txt -rl 150 -t 50 -auto-wildcard
```

Expected: resolving `<word>.example.com` hosts, with wildcard answers filtered out. Remember `-l` (resolve a list) and `-w` (bruteforce) are **mutually exclusive** — a bruteforce takes `-d` + `-w`, never `-l`. Keep the wordlist proportionate to the program; a million-word list through three resolvers is both slow and conspicuous.

---

## Pitfalls, noise and scope discipline

How hunters get reports rejected, get rate-limited into false conclusions, or stray out of scope with this tool:

- **The rate limit is OFF by default.** `-rl`/`-rate-limit` defaults to `-1` (disabled) and `-t`/`-threads` defaults to `100`. Run dnsx naked on a large list and it fires DNS as fast as the host and resolvers allow. That does three bad things: trips resolver rate limits (turning live hosts into `SERVFAIL` false negatives), looks like abuse from your egress IP, and — for uncached names in a bruteforce — pushes authoritative-query load onto the target's nameservers. Always set `-rl`. For most programs `-rl 100`–`150` with `-t 50` is both polite and fast enough.
- **Dirty resolvers manufacture findings.** A resolver that wildcard-answers or NXDOMAIN-hijacks will invent subdomains. Submitting an "asset" that only exists because your resolver lied is a fast way to lose credibility. Use a validated `-r` list and keep wildcard filtering on; re-resolve anything surprising through a second independent resolver (and `dig`) before believing it.
- **Wildcard floods.** Without `-auto-wildcard` (or `-wd`), a wildcard domain makes every bruteforced name "resolve." `-auto-wildcard` and `-wd` are **mutually exclusive**; when you use `-wd` the help text warns other flags are ignored and you should take `-json`. Tune with `-wt` (default 5) if filtering is too eager or too lax.
- **`-rcode` is whitespace-sensitive and fatal on error.** Valid values include `noerror, formerr, servfail, nxdomain, notimp, refused` (and raw numerics like `3`). The list is split on commas **with no trimming**, so `-rcode noerror,servfail` works but `-rcode "noerror, servfail"` (note the space) fails hard with `invalid rcode value` and aborts the run. No spaces.
- **`-stream` turns off the safety features.** Stream mode is faster but the help text is explicit: **wordlist, wildcard filtering, stats, and stop/resume are all disabled** under `-stream`. Don't use it when you care about wildcard noise or a resumable long job.
- **Takeover ≠ dangling CNAME.** A CNAME to a third party that is still live and owned is not a finding. Confirm claimability with a takeover fingerprinter (subjack/nuclei) and the service's own "no such bucket/app" response before reporting. And **never actually claim the resource** to prove it — registering someone's dangling S3 bucket or Heroku app can be destructive, disrupt real users, and violate both the program rules and the third party's ToS. Fingerprint, screenshot, stop.
- **AXFR and PTR hit target-operated infrastructure.** A/CNAME resolution lands on public recursive resolvers, but `-axfr`, `-ns`/`-soa` against the target's nameservers, and PTR sweeps of the target's IP space touch the *target's* infrastructure directly. Only run these against assets explicitly in scope, and keep the rate capped.
- **CDN IPs are not the target's host.** Treat `-cdn`-tagged answers as "do not port-scan" — the IP belongs to the CDN, scanning it is out of scope and often prohibited, and any "finding" there is about the CDN, not your target.
- **Resolution volume is still volume.** Even de-noised, a multi-thousand-name resolve is a burst of traffic. If a program caps request rates globally, your DNS stage counts. Default to passive-first (resolve enumerator output) before bruteforcing, and bruteforce with a proportionate wordlist.

---

## Reporting evidence

dnsx findings are DNS state, and DNS state changes — a dangling record can be fixed (or re-break) between your scan and the triager's. Capture enough that a triager reproduces it independently and timestamps are unambiguous:

- **The exact command, including resolvers and rate.** `dnsx -l … -cname -json -r resolvers.txt -rl 150` — so the triager sees you used vetted resolvers and a polite rate, and can rerun it.
- **The raw JSONL line.** For a takeover, the object carrying `host`, `cname`, `status_code`, `resolver`, and `timestamp` pins *which* resolver saw *what* and *when*. The `resolver` and `timestamp` fields are what defend the finding if the record is "fixed" before triage: "at 2026-11-16T09:12Z, resolver 1.1.1.1 returned CNAME → `abandoned.s3.amazonaws.com`, status NXDOMAIN."
- **An independent confirmation.** Re-prove the record with a second tool the triager trusts — `dig CNAME takeover.example.com +short` and `dig takeover.example.com` showing the same dangling target and NXDOMAIN. Never rest a report on a single tool's say-so.
- **The claimability proof, without claiming.** The third-party service's own response that the backing resource is gone (e.g. S3's `NoSuchBucket`, GitHub Pages' 404 project page), captured as the HTTP response body/headers — plus the subjack/nuclei fingerprint line that matched. Make clear you did **not** register the resource.
- **For AXFR:** the full `dnsx -axfr -resp` output (or the `dig axfr @ns1.example.com example.com` transcript) showing the nameserver served the zone, and which nameserver.
- **For internal-IP disclosure:** the JSONL line with `has_internal_ips: true` and the `internal_ips` array, plus a `dig` confirmation that the public name answers a private address from an external vantage point.
- **Scope and impact framing.** State the subdomain's role (does it host auth, cookies, user content?) because a takeover on `cookies.example.com` or an OAuth-callback host is Critical while one on a dead marketing microsite is Low. Cite the record type and the concrete takeover primitive, not just "subdomain takeover."

---

## References

- dnsx README (flags, examples, template fields, resolver notes) — https://github.com/projectdiscovery/dnsx
- dnsx official docs, usage/flag reference — https://docs.projectdiscovery.io/tools/dnsx/usage
- dnsx official docs, overview (record types, resolver formats, wildcard handling) — https://docs.projectdiscovery.io/tools/dnsx/overview
- dnsx source — `-rcode` value map (`noerror…nxdomain…badcookie`, comma-split, no trim) — https://github.com/projectdiscovery/dnsx/blob/main/internal/runner/options.go
- dnsx source — `ResponseData` JSON fields (`cdn-name`, `asn`, `query-time`) — https://github.com/projectdiscovery/dnsx/blob/main/libs/dnsx/dnsx.go
- retryabledns — `DNSData` JSON field names (`host`, `a`, `cname`, `status_code`, `has_internal_ips`, `internal_ips`, `resolver`, `timestamp`, `all`, `axfr`, …) — https://github.com/projectdiscovery/retryabledns/blob/main/client.go
- subjack (subdomain-takeover fingerprinting) — https://github.com/haccer/subjack
- nuclei takeover templates (`http/takeovers`) — https://github.com/projectdiscovery/nuclei-templates
- subfinder (upstream enumerator in the pipeline) — https://github.com/projectdiscovery/subfinder

*Research supplement added 2026-11-16, grounded in the dnsx README, the ProjectDiscovery docs (usage/overview), and the dnsx + retryabledns source (options.go, dnsx.go, client.go) cited above — every flag, JSONL field, and `-rcode` value verified against those sources (model: claude-opus-4-8).*
