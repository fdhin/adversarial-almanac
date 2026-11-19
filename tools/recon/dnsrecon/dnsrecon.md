---
name: "dnsrecon"
categories:
  - blackarch-recon
version: "1.6.3"
homepage: "https://github.com/darkoperator/dnsrecon"
packaged-by: [blackarch, kali, nixpkgs]
blackarch-builddate: "2026-06-29"
build-system: python
license: custom
meta-checked: "2026-11-09"
github-repo: "darkoperator/dnsrecon"
github-stars: 3088
github-pushed: "2026-11-03"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 243
gh-activity-checked: "2026-11-09"
---

# dnsrecon

> Classic all-round DNS enumeration: record pulls, zone-transfer tests, bruteforce, reverse lookups, cache snooping and DNSSEC zone walking.

## What it's for

dnsrecon (maintained by darkoperator) is the long-running Python workhorse for DNS reconnaissance during assessments and for troubleshooting DNS. One tool covers standard record enumeration (SOA, NS, A/AAAA, MX, SRV, TXT/SPF), AXFR testing against every name server, dictionary bruteforce of subdomains, PTR sweeps of IP ranges, search-engine and crt.sh enumeration, cache snooping against name servers, and NSEC-based DNSSEC zone walking. Results can be saved as JSON, CSV, XML or SQLite. It is a per-domain enumeration tool — for bulk-resolving long host lists through custom resolvers use dnsx or dnsprobe instead.

## Typical use cases

### Standard enumeration of a domain

```
dnsrecon -d example.com -t std
```

Outcome: prints a table of the domain's SOA, NS, A/AAAA, MX and common SRV records, checks for wildcard resolution, recursion and BIND version on each name server; add `-n 198.51.100.10` to direct every query at one specific server.

### Test every name server for zone transfer

```
dnsrecon -d example.com -t axfr
```

Outcome: attempts AXFR against each NS record of the domain and dumps the full zone contents on success — the single highest-value DNS misconfiguration check.

### Bruteforce subdomains with a dictionary

```
dnsrecon -d example.com -t brt -D subdomains.txt -f -j found.json
```

Outcome: resolves `<word>.example.com` for every dictionary entry, filters records that resolve to the wildcard IP out of what is saved (`-f`), and writes the found records to `found.json`.

### Reverse-resolve an IP range

```
dnsrecon -t rvl -r 198.51.100.0/24
```

Outcome: performs PTR lookups for every address in the CIDR and reports the hostnames found, revealing naming conventions and live hosts from IP space alone.

### Snoop a name server's cache

```
dnsrecon -t snoop -n ns.example.com -D hostnames.txt
```

Outcome: asks the given name server for every hostname in the file and reports which it answers from cache — evidence of recent queries for those names without sending traffic to the targets.

### Walk a DNSSEC-signed zone

```
dnsrecon -d example.com -t zonewalk
```

Outcome: follows the zone's NSEC records to enumerate every name inside a DNSSEC-signed zone and saves the recovered entries.

## Bug bounty workflow with dnsrecon

DNS reconnaissance is Phase 0 of almost every web bounty: it builds the subdomain and host map that every later test runs against, and it is where the two findings DNS can pay out directly — an open zone transfer and a dangling record that leads to subdomain takeover — are born. dnsrecon's strength is covering the whole DNS surface from one binary; that same breadth makes it easy to generate scope-violating or resolver-flooding traffic. Treat it as **passive-first, then narrowly active, always rate-capped**.

All targets below are `example.com`, `*.example.com`, or the `198.51.100.0/24` TEST-NET range. Run nothing against an asset that is not in the program scope — and remember that DNS recon pulls third-party infrastructure (shared name servers, CDN CNAMEs, mail providers) into view fast. Looking at their public DNS records is normally fine; *testing* a service you discover on them is not.

### Phase 0 — Authorization and scope pinning

- Confirm the apex domains and IP ranges you will touch are explicitly in scope. Write down the apex list and the CIDRs; everything dnsrecon emits beyond them is intel you may read but not probe.
- **AXFR discipline:** `-t axfr` talks only to the domain's own authoritative name servers (the NS records of the in-scope apex). That is the safe form. Never point `-n` at an arbitrary resolver or a provider's name server and then report the result as a target finding — the server that answered has to be the target's authoritative NS, in scope.
- **No resolver flooding:** `brt`, `rvl`, and `srv` are the loud scan types — they issue one query per candidate. Note the program's automation/rate rules before you run them and cap `--threads` accordingly.
- Record the dnsrecon version (`dnsrecon -V`) and the resolver you pin so every result is reproducible.

### Phase 1 — Passive enumeration (third parties, not the target's servers)

Lead with the scan *types* that query other people's data stores, so your first contact with the program's own infrastructure is near-silent:

```
dnsrecon -d example.com -t crt,bing,yand -j passive.json
```

`crt` pulls names from Certificate Transparency logs (crt.sh), `bing`/`yand` scrape the search engines. None of these three send a query to the target's authoritative name servers — they mine CT logs and search indexes. This is your seed subdomain list and the politest possible opening move. (Note the distinction from the modifier flags `-b`/`-y`/`-k`: those *add* a source to a full `std` enumeration, which does query the target. The bare `-t crt,bing,yand` types do not.)

Add WHOIS/netblock context when the program's scope is IP-based:

```
dnsrecon -d example.com -w -j whois.json     # deep WHOIS + reverse of WHOIS IP ranges (runs std too)
```

### Phase 2 — Baseline records and name-server posture

```
dnsrecon -d example.com -t std -j std.json
```

Pulls SOA, NS, A/AAAA, MX and common SRV records, and checks each name server for wildcard resolution, recursion, and BIND version — a handful of queries, low volume. Two outputs matter downstream: the **NS list** (that is your authorized AXFR target set) and the **wildcard verdict** (decides whether brute force will produce garbage — see Phase 4).

### Phase 3 — Zone transfer (the check that pays when it lands)

```
dnsrecon -d example.com -t axfr
```

Tests each authoritative NS for AXFR. On success you receive the entire zone — every record the operator put in it — in one TCP connection per server. This is the single highest-value, lowest-noise DNS check: a couple of connections, no brute force, and a complete internal map if it works. Capture the output and reproduce it with `dig` (see Reporting evidence) so the triager can verify it in one command.

### Phase 4 — Active expansion (only if needed, rate-capped)

If passive sources plus AXFR already mapped the surface, you may not need this at all. When you do, try the quiet enumerators before the loud one:

```
# DNSSEC/NSEC walk — enumerates a signed zone with far less traffic than brute force
dnsrecon -d example.com -t zonewalk -j zonewalk.json

# Dictionary brute force — thread-capped, longer timeout, wildcard hits filtered out
dnsrecon -d example.com -t brt -D subdomains.txt -f --threads 4 --lifetime 5 -j brute.json
```

`zonewalk` only works against DNSSEC-signed zones that use NSEC (not NSEC3), but when it applies it hands you the full name list for the cost of a walk. For `brt`, `--threads 4` holds the burst down, `--lifetime 5` tolerates a throttling resolver, and `-f` drops any hit that resolves to the wildcard address (see Pitfalls). Raise threads only if the program's rules explicitly allow heavier automation.

### Phase 5 — Reverse and SRV mining

```
dnsrecon -t rvl -r 198.51.100.0/24 --threads 4 -j ptr.json     # PTR sweep of an in-scope netblock
dnsrecon -d example.com -t srv -j srv.json                     # SRV service records
```

Reverse lookups across an in-scope CIDR reveal naming conventions and live hosts from IP space alone; SRV mining surfaces the service types an org runs (LDAP, Kerberos, SIP, autodiscover, XMPP), which is infrastructure intel for later phases. Keep threads low — a `/24` is 254 PTR queries; a `/16` is 65k, which is a flood unless the program sanctions it.

### Phase 6 — Triage DNS output into findings

The raw DNS data is rarely the finding by itself. Convert it:

- **AXFR success** → a direct information-disclosure finding; report it with the dig reproduction.
- **New subdomain with a dangling CNAME** (points at an unclaimed cloud/SaaS resource) → subdomain-takeover candidate. Verify the target resource is actually unclaimed before reporting; the CNAME alone is not the bug.
- **Internal hostnames / dev / staging names** → feed the web-app phases; the DNS name is the lead, the web finding is the payout.
- **Exposed IP space and SRV services** → recon intel; reportable on their own only as low-severity hygiene.

---

## Where the money is with dnsrecon

DNS findings are mostly recon multipliers — the real payouts are the web and takeover bugs they lead to. The exceptions (AXFR, takeover) are listed first.

| Finding class | dnsrecon command/feature | Typical severity | Dupe risk |
|---|---|---|---|
| Open zone transfer (AXFR) | `-t axfr` (or `-a` alongside `std`) | Medium–High | High on mature programs (first thing everyone tries); Medium on freshly-acquired assets |
| Subdomain takeover (dangling CNAME surfaced, then verified) | `-t brt` / `crt` / `zonewalk` → inspect CNAMEs, verify unclaimed | High | Medium — depends who enumerated the asset first |
| Forgotten dev/staging subdomain → new app surface | `-t brt,crt,zonewalk` | Varies (feeds every later phase) | Medium |
| Full zone enumeration via DNSSEC NSEC walk | `-t zonewalk` | Low–Medium | High |
| Internal hostnames / IP space disclosed | `-t axfr`, `-t rvl` | Low–Medium | High |
| SPF/MX netblock and service disclosure | `-s`, `-t srv`, `std` | Info–Low | High |
| Cache snooping (recent-query disclosure) | `-t snoop` | Info–Low (often out of scope) | — (frequently invalid in bounty) |

Rule of thumb: the only rows that stand alone as a report are AXFR and a *verified* takeover. Everything else is intel — valuable because it widens the surface for Phases where the bounties actually are.

---

## Command recipes

Every block below is a real dnsrecon invocation with the low-volume settings called out. Output snippets are illustrative of dnsrecon's `[*]`/`[+]`/`[-]` log style, not verbatim transcripts.

### Passive multi-source seed (no target queries)

```
dnsrecon -d example.com -t crt,bing,yand -j passive.json
```

```
[*] crt.sh: Enumerating subdomains for example.com
[*] 	 A www.example.com 198.51.100.10
[*] 	 A api.example.com 198.51.100.11
[*] 	 CNAME blog.example.com -> example.github.io
[*] Bing: Enumerating ...
[+] 17 records found, saved to passive.json
```

Queries CT logs and search engines only. Start here on every target. An empty result means "these sources had nothing," not "no subdomains exist" — the engines rate-limit and truncate.

### Standard baseline with structured output

```
dnsrecon -d example.com -t std -c std.csv -j std.json
```

```
[*] SOA ns1.example.com 198.51.100.53
[*] NS ns1.example.com 198.51.100.53
[*] NS ns2.example.com 198.51.100.54
[*] MX mail.example.com 198.51.100.25
[*] A example.com 198.51.100.10
[*] Checking for Wildcard resolution
[*] Checking for recursion / BIND version on each NS
```

The NS lines are your authorized AXFR set; the wildcard check gates Phase 4.

### Zone transfer against the authoritative servers

```
dnsrecon -d example.com -t axfr
```

Success:

```
[*] Checking for Zone Transfer for example.com name servers
[*] Resolving SOA / NS Records
[*] Trying NS server 198.51.100.53
[+] 198.51.100.53 Has port 53 TCP Open
[+] Zone Transfer was successful!!
[*] 	 A dev.example.com 198.51.100.20
[*] 	 A staging.example.com 198.51.100.21
[*] 	 TXT example.com "v=spf1 ..."
```

Refusal (the common, correct case):

```
[*] Trying NS server 198.51.100.53
[-] Zone Transfer Failed for 198.51.100.53
[-] No answer or RCODE for AXFR
```

One TCP connection per NS. If it succeeds, go straight to the `dig` reproduction below for the report.

### Brute force with wildcard and rate discipline

```
dnsrecon -d example.com -t brt -D /usr/share/wordlists/dnsrecon/subdomains.txt \
  -f --threads 4 --lifetime 5 -j brute.json
```

- `-f` filters out any hit that resolves to the wildcard IP when saving, so a `*.example.com` wildcard does not fill `brute.json` with false positives.
- `--threads 4` caps the concurrent query burst; `--lifetime 5` waits 5 s per query for a slow/throttling server (default is `3.0`).
- If dnsrecon detects a wildcard it prompts `Do you wish to continue? [Y/n]`. Answer deliberately. Use `--iw` to continue past a wildcard *without* the prompt only when you understand you will be resolving noise — pair it with `-f` so the saved set stays clean.

When `-D` is omitted, dnsrecon falls back to `/etc/dnsrecon/namelist.txt`, and if that is absent, the `namelist.txt` bundled with the Python package.

### Reverse-resolve an in-scope range

```
dnsrecon -t rvl -r 198.51.100.0/24 --threads 4 -j ptr.json
```

Accepts either `first-last` or `range/bitmask`. Keep the range in scope and the threads low — this is one PTR query per address.

### DNSSEC zone walk

```
dnsrecon -d example.com -t zonewalk -j zonewalk.json
```

Follows NSEC records to enumerate a signed zone. Cheaper and quieter than brute force; only works where NSEC (not NSEC3) is in use.

### Cache snooping (read the caveat in Pitfalls first)

```
dnsrecon -t snoop -n ns.example.com -D hostnames.txt --disable_recurs
```

`--disable_recurs` clears the recursion-desired flag so you are asking the server what it already holds in cache, not asking it to go fetch. Without it you populate the cache rather than observe it, and the result proves nothing. Snooping targets a *resolver* in scope — rarely the right move in a bounty (see Pitfalls).

### Multiple domains and output normalization

```
dnsrecon -iL apex-domains.txt -t std,crt -j all.json -c all.csv -x all.xml --db all.sqlite
```

`-iL` reads one domain per line. dnsrecon writes all four formats in one run: `-j` JSON, `-c` CSV, `-x` XML, `--db` a SQLite 3 file. JSON is the most practical for `jq` pivots; the SQLite DB is handy for diffing today's enumeration against last week's.

### Targeting and TCP

```
dnsrecon -d example.com -t std -n 198.51.100.53 --tcp --lifetime 5
```

`-n` pins the name server (comma-separated list accepted; defaults to the target's SOA server when omitted). `--tcp` forces TCP, useful where UDP is filtered or answers are truncated.

---

## Pitfalls, noise and scope discipline

**Thread floods against the target's resolvers.** `brt`, `rvl`, and `srv` issue one query per candidate. Left uncapped, `--threads` turns a wordlist into a burst that looks like an attack and can trip rate limits or get your source IP blocked. Cap threads (4–10 is plenty for recon), raise `--lifetime` instead of parallelism when servers are slow, and never run a `/16` reverse sweep unless the program explicitly permits that volume.

**Wildcard DNS makes every brute guess "resolve."** A `*.example.com` wildcard answers for every name you try, so an uncapped `brt` run produces a list of hundreds of non-existent "subdomains" — all pointing at the wildcard IP. dnsrecon detects this and prompts before continuing; always run brute force with `-f` so wildcard-IP hits are filtered from the saved output, and never report a wildcard-resolved name as a discovery. It is an instant invalid/dupe and it burns triager trust.

**AXFR against the wrong server is a scope violation, not a finding.** `-t axfr` is safe because it tests the apex's own authoritative NS. The moment you point `-n` at a third-party resolver or a provider's name server and call a transfer there a "target finding," you are both out of scope and reporting someone else's infrastructure. The server that answered the transfer must be the in-scope asset's authoritative NS.

**Cache snooping is usually the wrong tool in a bounty.** It targets a *resolver* and reveals what other users of that resolver have recently looked up — third-party browsing data, not a property of the target's authoritative DNS. The resolvers worth snooping are rarely the program's own assets, snooping them can expose unrelated users' data, and it needs non-recursive queries (`--disable_recurs`) to mean anything. Treat it as out of scope unless the program says otherwise.

**Passive sources rate-limit silently.** `-b`/`-y`/`-k` and `-t crt,bing,yand` hit Bing, Yandex, and crt.sh. Hammer them and you get captcha'd or throttled, and the result set truncates with no error. An empty or thin passive run is not evidence of a small attack surface — re-run later or corroborate with another source before concluding.

**Raw dumps are weak reports.** A full zone file or a 254-line PTR sweep pasted in with no analysis gets triaged as low-effort. Extract the specific misconfiguration (the transfer, the dangling CNAME, the internal host that should not be public) and lead with that.

**Resolver choice changes results.** Different resolvers return different (or cached, or filtered) answers. Pin `-n` to a known resolver for reproducibility, or to the authoritative NS for AXFR — and record which you used. A finding another hunter cannot reproduce because they used a different resolver will be closed.

---

## Reporting evidence

Capture structured output for every run (`-j`/`-c`/`--db`), and record the exact command line, the resolver or NS used, a timestamp, and the dnsrecon version (`dnsrecon -V`). Then, per finding:

**Open zone transfer.**
- The dnsrecon output line `[+] Zone Transfer was successful!!` and the NS that allowed it (name + IP).
- The independent `dig` reproduction — the canonical proof a triager will re-run:

  ```
  dig AXFR example.com @ns1.example.com
  ```

  On a vulnerable server this returns the full zone; on a correctly-configured one, `; Transfer failed.`. Include both the command and its output.
- A **sample plus a count** of disclosed records, not the entire zone if it is large or sensitive — enough to prove impact (internal/dev hostnames, non-public subdomains) without exfiltrating more than the proof needs.
- Impact framing: what the zone reveals and why it matters (hidden infrastructure, pre-production hosts, internal naming).

**Subdomain takeover candidate.**
- The CNAME record from dnsrecon's JSON showing the in-scope subdomain pointing at the dangling provider.
- The provider's unclaimed/"no such bucket/app" fingerprint (HTTP response or error page).
- State that the target resource is unclaimed; do **not** register or claim it unless the program's rules explicitly allow a PoC claim.

**General enumeration findings.**
- The structured output file (`found.json`/`std.csv`) as a reproducible artifact.
- The command, resolver, timestamp, and version so the result is independently repeatable.
- Only the slice of records that proves the point — a diff against a prior `--db` run is a clean way to show *what is new* on the asset.

---

## References

- dnsrecon GitHub repository — https://github.com/darkoperator/dnsrecon
- dnsrecon README — https://raw.githubusercontent.com/darkoperator/dnsrecon/master/README.md
- dnsrecon argument parser (`build_parser()` in `cli.py`, source of every flag above) — https://raw.githubusercontent.com/darkoperator/dnsrecon/master/dnsrecon/cli.py
- dnsrecon man page (generated from the CLI parser) — https://raw.githubusercontent.com/darkoperator/dnsrecon/master/man/dnsrecon.1
- `zonetransfer.me` — DigiNinja's intentionally AXFR-open domain, the man page's own `-t axfr` practice target (use for learning, never a program asset)

*Research supplement added 2026-11-16, grounded in the dnsrecon GitHub repo, README, `cli.py` argument parser, and man page (flags verified verbatim against `build_parser()`), model claude-opus-4-8.*
