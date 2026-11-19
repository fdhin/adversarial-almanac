---
name: "hakrawler"
categories:
  - blackarch-webapp
version: "237.52a16fe"
homepage: "https://github.com/hakluke/hakrawler"
packaged-by: [blackarch, kali, nixpkgs]
blackarch-builddate: "2026-08-24"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "hakluke/hakrawler"
github-stars: 5161
github-pushed: "2026-08-05"
github-archived: false
github-issues: 9
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# hakrawler

> Fast golang web crawler for gathering URLs and JavaScript file locations from a site and its subdomains.

## What it's for

hakrawler (by hakluke, built on gocolly) takes URLs on stdin and crawls them for links, forms and JavaScript file locations, printing everything it finds — endpoint and asset discovery for recon and bug bounty. Scope stays on the exact domain given by default; add `-subs` to widen to its subdomains, which also fixes the common no-results case where the apex 301s to `www`. It collects URLs, it does not probe whether they are alive or vulnerable — pipe its output to a probing tool for that.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Crawl a single site

Quick endpoint enumeration from one seed URL.

```
echo https://www.example.com | hakrawler
```

Outcome: prints every URL and JavaScript file location discovered up to the default crawl depth of 2.

### Include subdomains and crawl deeper

Widen scope when the app spans multiple hosts.

```
echo https://example.com | hakrawler -subs -d 3
```

Outcome: crawls example.com plus any `*.example.com` host encountered, to depth 3 — required when the apex redirects to a subdomain that would otherwise be out of scope.

### Route the crawl through an intercepting proxy

See and modify every request the crawler makes.

```
cat urls.txt | hakrawler -proxy http://127.0.0.1:8080 -insecure
```

Outcome: all crawl traffic for every URL in `urls.txt` flows through the local proxy with TLS verification disabled, so each request/response can be inspected or replayed.

### Structured, deduplicated output with provenance

Feed downstream tooling clean, attributable URLs.

```
echo https://www.example.com | hakrawler -u -s -json
```

Outcome: prints one JSON object per line for each deduplicated (`-u`) URL, tagged with where it was found (`-s`: href, form, script) — ready for grep/jq filtering.

---

## Bug bounty workflow with hakrawler

hakrawler's niche is "fast and dumb": it takes URLs on stdin, parses the HTML each one returns with gocolly, and prints the links, form actions and `<script src>` locations it finds, recursing to a bounded depth. It is a *link extractor over live HTML*, not a deep crawler — it will not render JavaScript, follow SPA routes, or parse endpoints out of the *contents* of a `.js` file (that is katana's `-jc`/`-jsl`, or a dedicated LinkFinder pass). What it buys you is speed and a trivially pipeable plain-text stream: one seed in, an attributed URL list out, in seconds. Treat it as the first wide sweep that feeds your probing and parameter tooling, then reach for a heavier crawler only where coverage matters.

The complete flag set (verified against the upstream README — there are exactly 14 flags, and there is **no** `-js`, `-jid`, `-depth`, `-o`, or any built-in Wayback/robots option; do not cite flags that do not exist):

| Flag | Meaning | Default |
|---|---|---|
| `-d int` | Crawl depth | `2` |
| `-t int` | Threads | `8` |
| `-timeout int` | Max seconds to spend per stdin URL (`-1` = no cap) | `-1` |
| `-size int` | Per-page size limit in KB (`-1` = no cap) | `-1` |
| `-subs` | Include subdomains of the seed in scope | off |
| `-i` | Only crawl *inside* the seed's path | off |
| `-dr` | Disable following HTTP redirects | off (redirects followed) |
| `-insecure` | Disable TLS verification | off |
| `-proxy string` | Route all traffic through a proxy URL | none |
| `-h string` | Custom headers, `;;`-separated (`-h "Cookie: a=b;;Referer: ..."`) | none |
| `-u` | Show only unique URLs (dedupe) | off |
| `-s` | Show the *source type* of each URL (href/form/script) | off |
| `-w` | Show the page the URL was found *on* (parent link) | off |
| `-json` | Emit one JSON object per URL | off |

### Phase 0 — Authorization and seed scoping

Before a single request: confirm the program's rules of engagement permit automated crawling, note any rate ceiling, and write down the exact in-scope apex(es) and whether subdomains are in scope. hakrawler has **no allow-list of its own** — `-subs` widens to *every* `*.example.com` host it encounters, and with redirects followed by default (no `-dr`) a seed can walk you straight onto an out-of-scope host or a third-party CDN. Scope is enforced by what you seed, `-subs`/`-i`, and by filtering the output — never assume the tool will stay in bounds.

```bash
# in-scope hosts, one per line — this file is your scope contract
cat > scope.txt <<'EOF'
app.example.com
api.example.com
EOF
```

### Phase 1 — Passive URL seeding (no traffic to the target yet)

hakrawler only ever sees what you pipe in. The highest-yield, lowest-noise move is to seed it from passive sources first — historical URLs from the Wayback Machine / Common Crawl via `gau` or `waybackurls` — so the subsequent live crawl starts from URLs that actually existed, not just the homepage. This is the "wayback integration" in practice: a pipe, not a flag.

```bash
# passive historical URLs -> live re-crawl of each, subdomains included
echo example.com | gau --subs | hakrawler -subs -u -s > crawl.txt
```

Outcome: `gau` emits historical URLs (which hakrawler would never reach by link-following alone — deleted pages, old parameters, orphaned endpoints), hakrawler then crawls each live and attributes what it finds. Passive-first keeps your live footprint small while maximising surface.

### Phase 2 — Bounded live crawl

Now crawl the live site under explicit caps. The two settings that keep you program-safe are `-t` (threads — the request-rate knob) and `-timeout` (so one slow or tar-pit host cannot hang the run):

```bash
cat scope.txt | sed 's#^#https://#' \
  | hakrawler -subs -d 2 -t 4 -timeout 10 -u -s -w > live.txt
```

Outcome: each in-scope host crawled to depth 2 with only 4 concurrent threads and a 10-second ceiling per seed, deduplicated, each line tagged with its source type (`-s`) and the page it was found on (`-w`). Start at `-d 2` (the default) and `-t 4`; raise depth only where a host is clearly under-covered. `-d 3+` with default `-t 8` on a large app is where hunters generate complaint-worthy traffic.

### Phase 3 — Triage the attributed output

With `-s` and `-w` on, every URL carries provenance. Split the stream by what it is before you probe anything:

```bash
# form actions — the state-changing / injectable surface
grep '\[form\]' live.txt | awk '{print $1}' | sort -u > forms.txt
# javascript assets — secondary extraction targets (Phase 4)
grep '\[script\]' live.txt | awk '{print $1}' | sort -u > js.txt
# everything with a query string — parameter testing candidates
grep '?' live.txt | awk '{print $1}' | sort -u > params.txt
```

(The exact bracket tokens depend on version; run one crawl with `-s` and eyeball the labels before scripting against them. Prefer `-json` and `jq` when you want stable field parsing.)

### Phase 4 — JavaScript asset follow-up

hakrawler gives you the *locations* of script files, not the endpoints buried inside them. That second extraction is a separate pass — fetch each `.js` and mine it:

```bash
# hakrawler finds the script URLs; a JS-aware tool mines paths from their bodies
cat js.txt | while read -r u; do curl -s "$u"; done \
  | grep -oE '"[/][a-zA-Z0-9_?&=/.-]+"' | tr -d '"' | sort -u > js-endpoints.txt
```

For anything beyond a grep, hand `js.txt` to a purpose-built extractor (LinkFinder, `xnLinkFinder`, or `katana -jc`). This is the boundary of hakrawler's job — know it and stop, rather than expecting flags it does not have.

### Phase 5 — Probe and validate (turn URLs into findings)

A URL list is not a finding. Pipe the deduplicated output through liveness, parameter, and vulnerability tooling, keeping each stage low-volume:

```bash
# which discovered URLs are actually live, with status + tech
cat live.txt | awk '{print $1}' | httpx -silent -sc -title -td > alive.txt
# classic gf/qsreplace pipeline for parameter-bearing URLs
cat params.txt | gf xss | qsreplace '"><svg onload=1>' | head
```

Outcome: `httpx` confirms what is reachable (so you do not report a 404 from a historical URL), and the `gf`/`qsreplace` pattern turns parameterised URLs into test cases for a *manual*, one-at-a-time review. hakrawler's plain, space-free, one-URL-per-line output is exactly what these filters expect — which is the whole reason it sits at the front of this pipeline.

---

## Where the money is with hakrawler

hakrawler itself finds *nothing vulnerable* — it finds **surface**. Its payouts are entirely in what the discovered URLs feed. Priority is by how reliably a given hakrawler feature surfaces a high-value lead:

| Finding class | hakrawler feature that reaches it | Typical severity | Dupe risk |
|---|---|---|---|
| Hidden/forgotten endpoints (old API versions, debug routes) | `gau \| hakrawler` passive-seeded crawl surfaces URLs link-following misses | High | Medium — popular targets are heavily gau'd |
| Parameterised URLs → IDOR / SQLi / XSS candidates | `-u` list filtered for `?` → `gf`/`qsreplace` | High–Critical (depends on the bug) | High on the URL; **low on the validated bug** |
| Secrets / internal endpoints in JS bundles | `-s` tags `[script]` URLs → secondary extraction (Phase 4) | High–Critical | Medium — many hunters grep JS |
| Form actions / CSRF & injection surface | `-s` `[form]` filter | Medium–High | Medium |
| Subdomain takeover / orphaned hosts | `-subs` reveals hosts referenced but unprobed → pipe to subjack/`httpx` | High | Medium |
| New endpoints shipped since last crawl | diff today's `-u` output against a stored baseline | varies — fresh code is under-reviewed | **Low** — you saw it first |

The repeatable edge is the last row: store every crawl's deduplicated output and `diff` runs over time. A newly appearing route is the least-reviewed code on the target and your best shot at a non-duplicate. The URL discovery is never itself the bounty — the validated bug behind it is, and that bug's dupe risk is governed by your testing, not by hakrawler.

---

## Command recipes

Every block below is annotated with its expected output and the low-volume setting that keeps it program-safe. Defaults shown are the upstream defaults.

**Single-seed smoke test (safe default).**
```bash
echo https://www.example.com | hakrawler
# -> links, form actions and script URLs to the default depth of 2, threads=8.
# No scope widening, no auth — the cheapest "does this work / is the host alive" check.
```

**Scoped deep crawl of one path only.**
```bash
echo https://app.example.com/account/ | hakrawler -i -d 3 -t 4 -timeout 10 -u
# -i confines the crawl to URLs under /account/; -d 3 goes deeper where it matters;
# -t 4 holds the rate down. Use -i to avoid dragging the whole app into one run.
```

**Subdomain-aware crawl, attributed and deduplicated.**
```bash
cat scope.txt | sed 's#^#https://#' | hakrawler -subs -u -s -w
# One run across every seed host; -subs follows *.example.com links; each URL tagged
# with source type (-s) and parent page (-w). This is the canonical recon run.
```

**Authenticated crawl through Burp, read-only discipline.**
```bash
echo https://app.example.com | hakrawler \
  -h "Cookie: session=$SESSION;;User-Agent: bugbounty-yourhandle" \
  -proxy http://127.0.0.1:8080 -t 2 -timeout 15 -u
# -h sends the session cookie (;;-separated headers) so the crawl sees authed pages;
# -proxy mirrors every request into Burp for later review; -t 2 is deliberately slow.
# WARNING: an authenticated crawl issues GETs to every link it finds — see Pitfalls.
```

**Machine-readable output for a pipeline.**
```bash
echo https://www.example.com | hakrawler -json -u -s \
  | jq -r 'select(.source=="script") | .url'
# -json gives stable fields instead of bracket tokens; jq pulls just the script URLs.
# Prefer this over grep when another tool consumes the result.
```

**Size cap on a bandwidth-sensitive or metered engagement.**
```bash
echo https://www.example.com | hakrawler -size 500 -t 4
# -size 500 skips parsing any page body over 500 KB (default -1 = no limit), bounding
# both memory and the bytes you pull from the target.
```

**Passive-only surface (no live crawl of the target at all).**
```bash
echo example.com | gau --subs | anew gau-urls.txt
# When the program is strict about automated traffic, stop here: gau/wayback data is
# entirely passive. Feed gau-urls.txt to httpx later, one controlled pass.
```

Rate guidance: `-t` is the only concurrency control (there is no requests-per-second flag). On a shared or production target keep `-t` at 2–4 and always set `-timeout` so a single host cannot stall or get hammered indefinitely. There is no `-delay` option — if you need gentler pacing than `-t 2` gives, crawl one seed at a time.

---

## Pitfalls, noise and scope discipline

- **`-subs` and default redirect-following walk you out of scope.** `-subs` admits *every* subdomain encountered, and because redirects are followed unless you pass `-dr`, a seed can bounce onto a sibling host, a vendor CDN, or a parked domain that is **not** in your program. Reports on out-of-scope hosts get closed as invalid and can cost you standing. Always post-filter output against your `scope.txt`, and use `-dr` when you want to see *where* a link points without chasing the redirect.
- **Crawling issues real GET requests — including to state-changing links.** hakrawler is a crawler; it fetches every link it discovers. If the app exposes GET-based actions (`/logout`, `/unsubscribe?u=…`, `/items/123/delete`, one-click confirm links), an unauthenticated crawl can trigger them and an **authenticated** crawl can log you out, consume single-use tokens, or mutate state. Before an authed crawl, know which GETs change state; prefer proxying through Burp (`-proxy`) so you have a record of exactly what was hit.
- **Thread floods read as abuse.** The default `-t 8` across many seeds, or `-d 3+` on a large app, can push request rates that trip WAFs and rate limiters and generate complaints. Drop to `-t 2–4` on production targets. hakrawler has no built-in rate limiter beyond thread count — restraint is your responsibility.
- **`-insecure` hides real TLS problems.** `-insecure` disables certificate verification purely to let the crawl proceed; it is a convenience for intercepting proxies, not a finding. Do not conflate "crawled with `-insecure`" with "the target has a TLS bug" — verify any certificate issue out-of-band.
- **Unbounded `-size`/`-timeout` can exhaust your own resources.** Both default to `-1` (no cap). A target serving multi-megabyte pages, or a tar-pit host, can balloon memory or hang the run. Set `-size` and `-timeout` on anything you do not control.
- **It does not parse inside JavaScript or render SPAs.** hakrawler lists `<script src>` locations; it does not read the endpoints *inside* those files, and it misses routes that only exist after client-side JS runs. Expecting it to find SPA/API routes leads to false "no endpoints" conclusions — follow up with a JS extractor or katana. Claiming hakrawler "found" a JS-buried endpoint it only linked to is an evidence error.
- **No robots/sitemap awareness.** It will happily crawl paths `robots.txt` disallows. That is fine under authorization, but it means scope is entirely on you — the tool offers no guardrail.
- **Historical URLs are not live URLs.** gau-seeded output includes long-dead paths. Reporting a 404'd historical URL as a "finding" wastes a triager's time — always confirm reachability with `httpx` before claiming anything exists.

---

## Reporting evidence

hakrawler's role in a report is the **discovery trail**, not the vulnerability itself. Capture:

- **The exact command**, including every scope-affecting flag (`-subs`, `-i`, `-d`, `-dr`) and your seed list, so the discovery is reproducible and the scope you crawled is unambiguous.
- **The attributed URL line** for the endpoint that led to the finding — run with `-s -w` (or `-json`) so you can show *where* the URL was found (href/form/script) and *on which page*. "`https://api.example.com/v1/old/export?id=1` — source: `href`, found on: `https://app.example.com/reports`" documents the discovery chain a triager can follow.
- **For JS-sourced leads:** the `[script]` URL from hakrawler **and** the separate extraction step (the curl + grep, or LinkFinder output) that pulled the endpoint/secret out of it — hakrawler only proves the script was referenced, not what it contained.
- **The validated request/response pair** for the actual bug, produced by your probing stage (Burp, `curl`, `httpx`). This, not the crawl, is the finding. hakrawler output is the appendix that shows how you got there.
- **A `diff` against a prior baseline** when the lead is a newly shipped endpoint — it substantiates "this route is new" and pre-empts a duplicate dispute.

Never paste a raw thousand-line crawl dump into a report. Include only the few attributed lines on the discovery path, and keep the full `-u -json` output as your own working artifact.

---

## References

- hakrawler README (flags, install, usage) — https://github.com/hakluke/hakrawler
- hakrawler releases / v2 tag — https://github.com/hakluke/hakrawler/releases
- gau (passive URL seeding) — https://github.com/lc/gau
- waybackurls — https://github.com/tomnomnom/waybackurls
- gf (grep patterns for URL triage) — https://github.com/tomnomnom/gf
- qsreplace (query-string substitution) — https://github.com/tomnomnom/qsreplace
- httpx (liveness/probe stage) — https://github.com/projectdiscovery/httpx
- anew (append-dedupe for baselines) — https://github.com/tomnomnom/anew

*Research supplement added 2026-11-16, grounded in the upstream hakrawler README and release pages at https://github.com/hakluke/hakrawler (flag set verified verbatim; the tool has no `-js`/`-jid`/`-usewayback` options — Wayback is composed via gau/waybackurls piping) (model: claude-opus-4-8).*
