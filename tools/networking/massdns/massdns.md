---
name: "massdns"
categories:
  - blackarch-networking
version: "316.6bfa471"
homepage: "https://github.com/blechschmidt/massdns"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-05-02"
build-system: make
license: GPL3
meta-checked: "2026-11-09"
github-repo: "blechschmidt/massdns"
github-stars: 3661
github-pushed: "2026-09-27"
github-archived: false
github-issues: 14
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 3
gh-activity-checked: "2026-11-09"
---

# massdns

> High-performance DNS stub resolver that resolves millions of domain names per run through a list of DNS resolvers.

## What it's for

MassDNS (by blechschmidt) is a C stub resolver built for volume: given a text file of domains and a text file of resolver IP addresses, it can resolve over 350,000 names per second by firing large numbers of concurrent UDP queries and multiplexing the replies. It is the standard engine behind large-scale subdomain enumeration, reverse-DNS sweeps and certificate-transparency mining — its shipped helper scripts generate candidate name lists that are piped straight into it. It is a stub resolver, not a recursive one: it needs that resolvers file (`-r`) and pushes the recursion onto the listed servers, so it is abusive to public resolvers by design. Output goes to stdout or `-w FILE`, with `-o` selecting formats from simple text (`S`) to ndjson (`J`).

## Typical use cases

### Bulk-resolve a list of domains

```
massdns -r /usr/share/massdns/lists/resolvers.txt -t A -w results.txt domains.txt
```

Outcome: resolves an A record for every name in `domains.txt` using the shipped public-resolver list and writes the answers to `results.txt` (redirecting stdout works equally well); `-t AAAA`, `-t PTR` etc. select other record types.

### Enumerate subdomains by brute force

```
/usr/share/massdns/scripts/subbrute.py example.com /usr/share/massdns/lists/names.txt | massdns -r /usr/share/massdns/lists/resolvers.txt -t A -o S -w results.txt
```

Outcome: `subbrute.py` expands `example.com` against the wordlist into candidate hostnames, and massdns resolves them in one pipeline, `-o S` keeping the output to simple `domain IP` lines.

### Sweep a network range with reverse DNS

```
/usr/share/massdns/scripts/ptr.py | massdns -r /usr/share/massdns/lists/resolvers.txt -t PTR -w ptr.txt
```

Outcome: `ptr.py` emits PTR query names for an address range, and massdns writes the resolved reverse mappings to `ptr.txt`.

### Harvest hostnames from certificate transparency

```
/usr/share/massdns/scripts/ct.py example.com | massdns -r /usr/share/massdns/lists/resolvers.txt -t A -o S -w results.txt
```

Outcome: `ct.py` pulls subdomains observed in CT logs for `example.com` and the pipeline resolves only those names, typically yielding a cleaner candidate list than pure brute force.

### Check for entries in a resolver's cache (DNS cache snooping)

```
massdns -r <(/usr/share/massdns/scripts/auth-addrs.sh example.com) --norecurse -o Je subdomains.txt
```

Outcome: with `--norecurse` and a resolvers file limited to the authoritative/resolver addresses being probed, massdns issues non-recursive queries; `-o Je` emits ndjson including one record per failed query, letting you distinguish names answered from cache.

## Bug bounty workflow with massdns

massdns is not a vulnerability scanner — it is the resolution *engine* under a subdomain-enumeration pipeline. Its bounty value is turning a huge candidate name list into a small set of *live* hosts (and the CNAME/PTR facts attached to them) fast enough to be useful, without abusing either the target's authoritative nameservers or the third-party resolvers it borrows. Because it is a stub resolver (`-r` is mandatory; it pushes recursion onto the listed servers), **every query has two footprints**: the public resolver that forwards it, and the target's authoritative DNS that ultimately answers it. A brute-force run is, from the target's authoritative NS perspective, an NXDOMAIN flood. Treat the whole workflow as rate-sensitive.

### Phase 0 — Authorization and resolver hygiene (before a single query)

1. **Confirm active DNS brute force is in scope.** Many programs scope "subdomains of `*.example.com`" but prohibit high-volume active enumeration, or cap request rates. Passive discovery (CT logs, public datasets) is always safer and usually sufficient — reach for massdns brute force only when the program permits active probing.
2. **Do not use the shipped resolver list as-is.** Upstream is explicit: the bundled `lists/resolvers.txt` is "currently outdated with a large share of resolvers being dysfunctional," the resolvers "are not guaranteed to be trustworthy," and "if the authenticity of results is highly essential, you should not rely on the included resolver list." A dysfunctional or hijacked resolver produces false positives (poisoned answers) and false negatives (timeouts), which waste your triage time and can put *wrong* hostnames in a report.
3. **Build two resolver files.** A large *bulk* list — freshly validated with a tool like [dnsvalidator](https://github.com/vortexau/dnsvalidator) so every entry actually resolves a known control name correctly — for throughput, and a tiny *trusted* list (`1.1.1.1`, `8.8.8.8`, `9.9.9.9`, or a local Unbound) for the verification pass in Phase 4. Never report a hostname seen only through an unvetted bulk resolver.
4. **Establish the wildcard baseline.** massdns has **no built-in wildcard detection** — this is the single most important thing to know about it. Resolve a few random, guaranteed-nonexistent names first and record the IPs a wildcard returns, so you can subtract them later (Phase 3).

```bash
# Learn the wildcard answer BEFORE brute forcing (printf feeds names on stdin)
printf 'zzc9q1x.example.com\nqp7r2mk.example.com\n' \
  | massdns -r trusted-resolvers.txt -t A -o S -q
# Any IP returned here is a wildcard IP: every brute-forced name that resolves
# to it is noise, not a real host.
```

### Phase 1 — Passive-first candidate generation

Generate the name list with the cheapest sources first, dedupe, and only then brute force:

```bash
# Passive: CT logs cost the target zero queries (ct.py scrapes crt.sh)
/usr/share/massdns/scripts/ct.py example.com | sort -u > candidates.txt

# Active brute force, appended only if the program allows it
/usr/share/massdns/scripts/subbrute.py example.com \
  /usr/share/massdns/lists/names.txt | sort -u >> candidates.txt
sort -u candidates.txt -o candidates.txt
```

`names_small.txt` ships alongside `names.txt` for a lighter, lower-footprint brute set. In practice, combine massdns's CT output with external passive sources (subfinder, amass passive, etc.), dedupe the union, and hand *that* list to massdns as the resolver step — resolution is the one job massdns does better than anything else.

### Phase 2 — Controlled resolution

Resolve the deduped list with concurrency capped well below the default, so the target's authoritative NS and your borrowed resolvers never see an attack-shaped spike:

```bash
massdns -r bulk-resolvers.txt -t A -o S -w resolved.txt \
        -s 1000 -c 30 -i 500 -q candidates.txt
```

`-s 1000` caps in-flight lookups at a tenth of the default; `-c 30` and `-i 500` govern retry behavior. See Command recipes for the footprint math behind these numbers.

### Phase 3 — Wildcard filtering and correctness

Subtract the Phase 0 wildcard IPs, or — simpler and more correct for multi-level wildcards — let a wrapper that *does* understand wildcards do the whole brute-force-plus-filter in one step (see the shuffledns recipe). Raw massdns output on a wildcard domain is mostly garbage; filtering is not optional.

### Phase 4 — Verify survivors against trusted resolvers

Re-resolve the filtered survivors through the *trusted* resolver file (or hand them to dnsx with a true rate cap). Upstream's own "result authenticity" guidance is to re-check found names against resolvers you trust; a name that resolves through a bulk resolver but not a trusted one is either dead or poisoned.

### Phase 5 — Triage to actual findings

Live hosts are raw material, not findings. Pipe the verified list onward — `httpx` for live web services, a CNAME pass (`-t CNAME`) to hunt dangling records (subdomain takeover), PTR sweeps to map in-scope ranges. The finding is always the thing *behind* the DNS fact.

## Where the money is with massdns

massdns surfaces attack surface; the payout comes from what resolution reveals. Priority by typical bounty value:

| Finding class | massdns feature / command that reaches it | Typical severity | Dupe risk |
|---|---|---|---|
| Subdomain takeover (dangling CNAME → unclaimed SaaS) | `-t CNAME -o S`, then check each target for an unregistered service | High | **High** — the most-automated bug class in bounties; be fast or be a dupe |
| Forgotten dev/staging/admin hosts | brute force → `-t A`, then probe each live host | Medium–High (depends on what's behind) | Medium |
| Infra/zone mapping (NS, MX, TXT, SPF/DMARC) | `-t NS`, `-t MX`, `-t TXT -o S` over the apex and subs | Info–Low | High |
| In-scope IP range enumeration via reverse DNS | `ptr.py \| massdns -t PTR` over an authorized range | Low–Info (scope intel) | Medium |
| DNS cache snooping (what a resolver has looked up) | `--norecurse -o Je` against a specific resolver | Low–Medium | Low |
| Wildcard-masked real hosts | Phase-0 baseline + filtering exposes hosts hidden in wildcard noise | Feeds every row above | n/a |

Rows 1–2 are where the money is; rows 3–5 are reconnaissance that feeds other findings. Note the dupe risk on takeovers: CNAME enumeration is trivially automatable, so hundreds of hunters run it hourly on popular programs — the resolution has to be fast *and* followed immediately by the takeover check, or someone else reports it first.

## Command recipes

Every recipe below calls out the low-volume setting. The governing fact: **massdns has no requests-per-second flag.** Rate is controlled indirectly by `-s/--hashmap-size` (the number of concurrent in-flight lookups, default **10000**), with `-i/--interval` and `-c/--resolve-count` shaping retries. `--processes` defaults to 1 and the README describes massdns as "a simple single-threaded application," so throughput is almost entirely a function of `-s`.

### Footprint math — why the default is too hot for bounty work

```
in-flight queries        ≈ s            (-s / --hashmap-size)
sustained lookups/sec    ≈ s ÷ mean-resolver-RTT
per-resolver concurrency ≈ s ÷ (resolvers in -r file)
```

With the default `-s 10000` and ~50 ms resolver RTT, that is ~**200,000 attempted lookups/sec**, and nearly all of a brute-force run's queries are for the target zone, so the target's authoritative nameservers absorb close to that full rate as NXDOMAIN traffic. Spread across a 50-resolver file, each public resolver sees ~200 concurrent queries from you — enough to look like abuse and get your source IP null-routed. On a bounty target, **lower `-s`, never raise it**: `-s 1000` or `-s 500` keeps you useful without looking like a flood.

### Bulk-resolve with a safe rate cap

```bash
massdns -r bulk-resolvers.txt -t A -o S -w resolved.txt \
        -s 1000 -q candidates.txt
# -s 1000 : ~1/10th the default in-flight rate
# -o S    : simple "name. TYPE data" lines, easy to parse
# -q      : suppress the live status line (keeps -w/stdout clean)
# -> resolved.txt: www.example.com. A 198.51.100.23
```

### Parse massdns output into a clean host list

`-o S` lines are `name. TYPE data`; strip to resolved names:

```bash
# Names that returned an A record, trailing dot removed, deduped
awk '$2=="A"{sub(/\.$/,"",$1); print $1}' resolved.txt | sort -u > live-hosts.txt

# Structured alternative: -o J emits one ndjson object per name.
# Field names vary by build — sample one line first, then jq accordingly:
massdns -r bulk-resolvers.txt -t A -o J -s 1000 -q candidates.txt \
  | jq -r 'select(.status=="NOERROR") | .name' | sed 's/\.$//' | sort -u
```

`live-hosts.txt` is what you feed to `httpx`, or back into dnsx for record-type enrichment under a real rate limit.

### Hunt dangling CNAMEs (subdomain takeover candidates)

```bash
massdns -r trusted-resolvers.txt -t CNAME -o S -s 500 -q live-hosts.txt \
  | awk '$2=="CNAME"{print $1, $3}'
# -> app.example.com. s3-website.amazonaws.com.   <- check if the target is unclaimed
```

Use the *trusted* resolver file here (`-s 500`): a takeover finding rides on the CNAME being accurate, so you do not want a bulk-resolver's stale answer.

### Reverse-DNS sweep of an authorized range

```bash
# TEST-NET-2 range (198.51.100.0/24) stands in for an in-scope CIDR
/usr/share/massdns/scripts/ptr.py | massdns -r trusted-resolvers.txt \
  -t PTR -o S -w ptr.txt -s 500 -q
```

`ptr.py` deliberately emits addresses in non-ascending order to avoid hammering a single nameserver in a burst. Keep `-s` low — PTR sweeps still generate one query per address.

### DNS cache snooping against one resolver

```bash
massdns -r <(/usr/share/massdns/scripts/auth-addrs.sh example.com) \
        --norecurse -o Je -s 200 subdomains.txt
# --norecurse : non-recursive queries — answered only from cache
# -o Je       : ndjson, 'e' writes a record for each terminal query failure,
#               so you can tell "answered from cache" from "not cached"
```

### Resolver vetting (test which resolvers actually work)

```bash
# --predictable walks the resolver file in order; --sticky keeps the same
# resolver across retries, so you can attribute failures to a specific server
massdns -r candidate-resolvers.txt -t A --predictable --sticky \
  -o S -s 100 known-good-names.txt
# Pair with --verify-ip to drop replies whose source IP doesn't match the
# resolver you queried (a basic anti-spoofing / off-path-poisoning guard).
```

### Resolve IPv6 and multiple record types

```bash
# AAAA records for the live set (IPv6-only hosts are a frequently-missed surface)
massdns -r bulk-resolvers.txt -t AAAA -o S -s 1000 -q live-hosts.txt -w aaaa.txt

# NS/MX/TXT in separate passes — one type per run; there is no "all types" flag
for rt in NS MX TXT; do
  massdns -r trusted-resolvers.txt -t "$rt" -o S -s 500 -q live-hosts.txt -w "$rt.txt"
done
```

### Target-specific resolvers per name (`--extended-input`)

When you already know which authoritative server should answer a given name (e.g. probing a delegated zone), attach resolvers to the name itself; massdns tries those before falling back to `-r`:

```bash
# "name<space>resolver1 resolver2" per line; -r is still required as fallback
printf 'ns.sub.example.com 198.51.100.53\n' \
  | massdns -r trusted-resolvers.txt --extended-input -t A -o S -q
```

### Spread source footprint across an IPv6 /64 (when authorized and available)

```bash
# Rotates the source IPv6 per query from a subnet you control — spreads load
# so no single src IP looks like a flood. Only on ranges you are allowed to use.
massdns -r bulk-resolvers.txt --rand-src-ipv6 2001:db8:abcd::/64 \
  -t A -o S -s 1000 -q candidates.txt
```

This changes *your* source address, not the query rate — it is not a substitute for lowering `-s`. The target's authoritative NS still sees the same aggregate query volume.

### End-to-end, program-safe pipeline

```bash
# 1. passive + capped brute -> dedup
{ /usr/share/massdns/scripts/ct.py example.com;
  /usr/share/massdns/scripts/subbrute.py example.com \
    /usr/share/massdns/lists/names_small.txt; } | sort -u > candidates.txt
# 2. resolve at 1/10th default rate against a VALIDATED bulk list
massdns -r bulk-resolvers.txt -t A -o S -s 1000 -q candidates.txt -w resolved.txt
# 3. extract names, subtract the Phase-0 wildcard IP, verify through trusted resolvers
awk '$2=="A"{sub(/\.$/,"",$1); print $1}' resolved.txt | sort -u > live-hosts.txt
dnsx -l live-hosts.txt -r trusted-resolvers.txt -a -resp -rl 50 -o verified.txt
# 4. takeover sweep + web probe (the actual findings live here)
massdns -r trusted-resolvers.txt -t CNAME -o S -s 500 -q live-hosts.txt | awk '$2=="CNAME"'
httpx -l live-hosts.txt -title -status-code -tech-detect
```

### When to use massdns direct vs. the shuffledns wrapper vs. dnsx

| You want… | Reach for | Why |
|---|---|---|
| Maximum raw resolution throughput, custom output parsing, PTR sweeps, CT mining | **massdns** directly | It is the engine; the wrappers add overhead you may not need |
| Brute force **with wildcard filtering handled for you**, clean subdomain output | **shuffledns** | It shells out to the massdns binary and layers multi-level wildcard detection on top — the thing massdns itself lacks |
| A true **requests/second rate cap**, record-type probing, no compiling C, pipeline-native | **dnsx** | Pure-Go, built-in `-rl/-rate-limit` (massdns has none) and `-wd/-wildcard-domain` filtering |

```bash
# shuffledns: brute + wildcard filtering in one step, massdns under the hood.
# -t here is shuffledns's "concurrent massdns resolves" (maps to massdns -s);
# lower it for bounty politeness. -mcmd passes raw flags through to massdns.
shuffledns -d example.com -mode bruteforce -w names.txt \
  -r bulk-resolvers.txt -m "$(command -v massdns)" \
  -t 1000 -mcmd '-i 500' -o shuffle-out.txt

# dnsx: re-resolve massdns survivors through trusted resolvers WITH a hard
# rate cap massdns cannot give you.
dnsx -l live-hosts.txt -r trusted-resolvers.txt -a -resp -rl 50 -json -o verified.json
```

shuffledns requires the massdns binary installed (auto-detected at `/usr/bin/massdns` or `/usr/local/bin/massdns`, else pass `-m`); dnsx has no massdns dependency at all.

## Pitfalls, noise and scope discipline

- **The shipped resolver list is a trap.** Upstream itself flags it as outdated, largely dysfunctional, and untrustworthy. Reports built on its answers contain false positives (poisoned/parked IPs) and false negatives (timeouts read as NXDOMAIN). Validate a fresh resolver list every run; keep a separate trusted list for the final answer.
- **No wildcard detection = garbage findings.** Against a wildcard zone, raw massdns says *every* name resolves. Report a "discovered subdomain" that is really a wildcard catch-all and you look careless and get the report closed. Always baseline wildcards (Phase 0) or use a wrapper that filters them.
- **The default `-s 10000` is an abuse profile.** On a bounty target it floods the authoritative nameservers with NXDOMAIN and overloads the public resolvers you are borrowing — potentially a scope/ToS violation and a DoS, and a fast route to getting your IP null-routed. Dial `-s` down, not up. There is no rate-limit flag to save you; `-s` *is* the throttle.
- **Borrowed resolvers are third parties, not your target.** Public resolvers are infrastructure owned by Google, Cloudflare, Quad9, ISPs, and random open servers — none of them are in your bounty scope. Hammering them for throughput is abusing a non-target. If you need volume, run your own Unbound and point `-r` at it.
- **Results are not authenticated.** massdns trusts whatever the resolver returns; an off-path or malicious resolver can inject answers. `--verify-ip` only checks the reply's source IP matches the queried resolver — it does not prove the *answer* is honest. Re-verify anything you report through trusted resolvers.
- **PTR sweeps need an authorized range.** Only sweep CIDRs the program explicitly includes. A reverse-DNS sweep of a range you were not granted is out-of-scope scanning regardless of how "passive" it feels.
- **Combining it with aggressive brute wordlists multiplies the footprint.** A 2-million-line wordlist at default `-s` is millions of queries at the target's authoritative NS in seconds. Prefer `names_small.txt`, passive-first candidate generation, and a capped `-s`.

## Reporting evidence

For a DNS-derived finding, capture the chain from query to conclusion so a triager can reproduce it without re-running your whole pipeline:

- **The exact command**, including the resolver file used and the `-s`/`-i`/`-c` values — this proves you resolved at a responsible rate and against which resolvers.
- **The resolver file itself** (or a note that it was a single trusted resolver like `1.1.1.1`), so the answer is attributable and reproducible. A finding seen only through an anonymous bulk resolver is not credible evidence.
- **The raw massdns output line** for the finding, verbatim: e.g. `app.example.com. CNAME unclaimed-bucket.s3-website.amazonaws.com.` for a takeover candidate, plus the follow-up proof that the CNAME target is actually claimable.
- **The wildcard baseline**, showing the IP(s) a random nonexistent name returns, to pre-empt the "that's just a wildcard" rejection — demonstrate your reported host resolves to something *other* than the wildcard IP.
- **An independent confirmation** from a trusted resolver (`dig app.example.com @1.1.1.1` or a `dnsx -r trusted` line), proving the answer is not a single bad resolver's fiction.
- For **cache snooping**, the `--norecurse` query and the ndjson showing a cached vs. non-cached distinction, plus the specific resolver IP probed.

Keep rate evidence in the report by default: stating that you ran with `-s 500` against a validated resolver list shows the program you did not DoS their authoritative DNS — useful if they review traffic logs after the fact.

## Flag and output-format reference

Every entry below is from the README usage block of the packaged build (`316.6bfa471`; the README itself ships no semver string). Flags not listed in the official usage block do not exist — notably **there is no `-n`/threads flag and no rate-limit flag**; concurrency is `-s` and `--processes` only.

| Flag | Long form | Meaning / default |
|---|---|---|
| `-r` | `--resolvers` | Text file of DNS resolvers (**required**) |
| `-t` | `--type` | Record type (default `A`) |
| `-o` | `--output` | Output format flags (see below) |
| `-w` | `--outfile` | Write to file instead of stdout |
| `-s` | `--hashmap-size` | Concurrent in-flight lookups (default **10000**) — the throughput/footprint knob |
| `-c` | `--resolve-count` | Resolve attempts per name before giving up (default 50) |
| `-i` | `--interval` | ms between repeat resolves of the same name (default 500) |
| `-b` | `--bindto` | Bind to IP:port (default `0.0.0.0:0`) |
| `-q` | `--quiet` | Quiet mode (suppress the live status line) |
| `-l` | `--error-log` | Error log path (default `/dev/stderr`) |
| | `--processes` | Resolver processes (default 1; tool is otherwise single-threaded) |
| | `--socket-count` | Sockets per process (default 1) |
| | `--norecurse` | Non-recursive queries — DNS cache snooping |
| | `--retry` | Response codes treated as retryable (default: all but NOERROR/NXDOMAIN) |
| | `--sticky` | Keep the same resolver across retries |
| | `--predictable` | Walk the resolver list in order — resolver testing |
| | `--verify-ip` | Drop replies whose source IP ≠ the queried resolver (anti-spoof) |
| | `--flush` | Flush the output file on every response (streaming into a pipe) |
| | `--filter` / `--ignore` | Only output / suppress a given response code |
| | `--rand-src-ipv6` / `--rand-src-ipv6-file` | Random source IPv6 per query from a subnet/file |
| | `--sndbuf` / `--rcvbuf` | Send/receive socket buffer sizes |
| | `--status-format` | Live status format: `json` or `ansi` (default ansi) |
| | `--extended-input` | Each input name may carry its own space-separated resolvers, tried before `-r` |
| | `--root` | Do not drop privileges when run as root (not recommended) |

**Output format flags (`-o`):** `L` domain-list, `S` simple text, `F` full text, `B` binary, `J` ndjson. Simple-mode (`S`) sub-modifiers combine in one argument — `d` additional section, `i` indent, `l` line-feed between replies, `m` only records matching the question name, `n` answer section, `q` print the question, `r` prepend resolver IP + timestamp + return code, `s` line-feed between packet sections, `t` include TTL/class, `u` authority section. ndjson (`J`) takes `e` = emit a record per terminal query failure. Example: `-o Smt` gives simple output, answer-matching-question-only, with TTL/class.

## References

- massdns — official repository and README (usage/flags, resolver-list and result-authenticity guidance, bundled scripts) — https://github.com/blechschmidt/massdns
- massdns README (raw) — https://raw.githubusercontent.com/blechschmidt/massdns/master/README.md
- massdns man page — `doc/massdns.1` in the repository — https://github.com/blechschmidt/massdns/blob/master/doc/massdns.1
- shuffledns (ProjectDiscovery) — massdns wrapper with wildcard handling; CLI flags and massdns dependency — https://github.com/projectdiscovery/shuffledns
- dnsx (ProjectDiscovery) — standalone Go DNS toolkit; `-rl` rate limit, `-wd` wildcard-domain filtering, record-type flags — https://github.com/projectdiscovery/dnsx
- dnsvalidator (resolver-list vetting) — https://github.com/vortexau/dnsvalidator

*Research supplement added 2026-11-16, grounded in the official massdns repository/README (github.com/blechschmidt/massdns) and the ProjectDiscovery shuffledns and dnsx repositories; flags verified against the README usage block (model: claude-opus-4-8).*
