---
name: "subjack"
categories:
  - blackarch-scanner
version: "182.49c51e5"
homepage: "https://github.com/haccer/subjack"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2021-11-24"
build-system: go
license: Apache
meta-checked: "2026-11-09"
github-repo: "haccer/subjack"
github-stars: 2126
github-pushed: "2026-10-21"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 26
gh-activity-checked: "2026-11-09"
---

# subjack

> Go scanner for subdomain takeover: CNAME chains, NS delegations, stale A records, AXFR and mail.

## What it's for

Subjack (haccer) reads a list of subdomains and flags takeover opportunities
well beyond the classic dangling CNAME: expired nameserver domains and
dangling cloud DNS zones, stale A records pointing at dead cloud-provider
IPs, open zone transfers including nameserver bruteforce, SPF `include:` and
MX takeovers for email interception, SRV records, multi-level CNAME chains
(followed automatically up to 10 hops deep), and NXDOMAIN registration
confirmed via RDAP. Concurrency (`-t`) and timeouts are tunable, output goes
to a file with `-o` (`.json` extension for JSON), and domains can arrive on
stdin for piping. As with every takeover scanner, verify findings manually
before claiming them.

## Typical use cases

### Sweep a wordlist for CNAME takeovers

The standard mass check.

```
subjack -w subdomains.txt -t 100 -timeout 30 -ssl -o results.txt
```

Outcome: every subdomain in the file is resolved and fingerprinted over
HTTPS; hijackable services are flagged and written to results.txt (use a
.json extension for JSON output).

### Pipe straight from an enumerator

Chain discovery into checking without temp files.

```
subfinder -d example.com | subjack -ssl -a
```

Outcome: domains arriving on stdin are checked; `-a` sends requests to every
URL rather than only those with an identified CNAME, catching takeover
candidates the CNAME shortcut misses.

### Check nameserver takeovers

Find expired or dangling DNS delegations.

```
subjack -w domains.txt -ns -o results.json
```

Outcome: NS records are tested for expired registrable nameserver domains and
for zones delegated to Route 53, Google Cloud DNS, Azure DNS, DigitalOcean,
Vultr or Linode whose hosted zone no longer exists (detected via SOA
SERVFAIL/REFUSED probing).

### Hunt zone transfers and email takeovers

Pull the DNS and mail checks into one pass.

```
subjack -w domains.txt -axfr -mail -o results.json
```

Outcome: AXFR is attempted against official and bruteforced nameserver
hostnames (findings name the vulnerable server and record count), and SPF
`include:` / MX targets are checked for expired, registrable domains that
would enable spoofing or mail interception.

---

## Bug bounty workflow with subjack

Subjack is a candidate-finder, not a confirmer. It tells you a DNS record
*points at a resource that nobody appears to own*; it does not prove you can
claim that resource, and it cannot prove impact. The payout lives entirely in
the manual confirmation step. Treat every subjack hit as a lead to verify, run
the phases in order, and never report a "possible" line as a finding.

**Authorization first.** Subdomain takeover is one of the few bug classes
where the proof-of-concept *changes third-party state* — you register a
GitHub Pages repo, create an S3 bucket, or claim a Heroku app. Many programs
have an explicit rule about this: some require you to actually claim and serve
a benign marker, others forbid claiming and want the dangling record reported
for them to confirm internally. Read the program's takeover policy before you
run anything. When in doubt, stop at the dangling-record + fingerprint
evidence and ask triage how they want it confirmed.

### Phase 0 — Scope the asset list (passive)

Takeover scanning is only as good — and only as safe — as the list you feed
it. Build the subdomain list from your enumeration stage (subfinder, amass,
`crt.sh`, etc.), then **filter it to in-scope roots** before subjack ever sees
it. The common way hunters get a report closed as out-of-scope is piping a raw
enumerator straight into subjack and firing at acquisitions, partner domains,
or wildcard noise the program never authorized.

```
subfinder -d example.com -silent | sort -u > scope.txt
# prune anything not under an in-scope apex before scanning
```

### Phase 1 — Resolve and map CNAME chains

Run subjack read-first. The default check resolves every name and follows
CNAME chains automatically up to 10 hops deep, so a record that points
`a.example.com → b.example.net → <unclaimed service>` is still caught at the
end of the chain. Add `-a` so subjack sends a request to **every** URL rather
than only names that already carry an identified CNAME — that catches dangling
A-record and non-CNAME cases the shortcut misses (the README marks `-a` as
*recommended*). Keep threads modest and give it real resolvers:

```
subjack -w scope.txt -a -ssl -t 20 -timeout 30 -r resolvers.txt -o cname.json
```

The output of this phase is a shortlist of names whose target looks
unclaimed. Nothing here is a finding yet.

### Phase 2 — Classify each candidate by service class

Every candidate falls into one of subjack's detection classes, and each class
has a different confirmation path and a different severity ceiling:

- **Dangling CNAME → claimable SaaS** (default check): the classic case.
- **NS delegation takeover** (`-ns`): expired nameserver domain, or a cloud
  DNS zone (Route 53, Google Cloud DNS, Azure DNS, DigitalOcean, Vultr,
  Linode) that is delegated but no longer hosted — detected when the
  authoritative nameservers return SERVFAIL/REFUSED to an SOA probe.
- **Stale A record** (`-ar`): an A record pointing at a dead cloud IP (AWS,
  GCP, Azure, DigitalOcean, Linode, Vultr, Oracle). Flagged `STALE A RECORD`;
  the README itself says these must be verified manually because a
  non-responding IP is not always reclaimable.
- **Open zone transfer** (`-axfr`): AXFR against official and brute-forced
  nameserver hostnames. Flagged `ZONE TRANSFER` with the vulnerable server and
  record count.
- **Expired SPF `include:` / MX** (`-mail`): email spoofing or interception.
- **SRV takeover** (`-srv`): service-location records (SIP, XMPP, LDAP,
  Kerberos, IMAP, CalDAV).

For the CNAME class, the single most important triage step is cross-checking
the matched service against **EdOverflow's can-i-take-over-xyz** corpus, which
subjack's README cites as the fingerprint reference. That list carries a
status per service — *Vulnerable*, *Edge case* (claimable only under specific
conditions), or *Not vulnerable* (the provider now verifies domain ownership,
e.g. the AWS/CloudFront and modern Shopify changes). A dangling CNAME to a
*Not vulnerable* service is not a finding, no matter what a stale fingerprint
says. For the `-ns` class, the companion reference is
**indianajson/can-i-take-over-dns**.

### Phase 3 — Confirm the takeover manually

The fingerprint match is necessary, never sufficient. Confirm the chain by
hand and only then (if the program permits) claim:

```
dig +trace CNAME staging.example.com        # see the real chain and where it dies
dig SOA delegated-zone.example.com @ns-1234.example-dns.net   # NS class: SERVFAIL/REFUSED?
curl -s https://staging.example.com          # read the provider's "no such site" body
```

If claiming is allowed: register the resource on the third-party service
(the exact repo/bucket/app name the CNAME expects), serve a **unique, benign
proof token at a nonce path** — e.g. a text file at
`/.bug-bounty-poc-<random>.txt` containing your handle and the date — screenshot
it, then **relinquish the resource immediately**. Never serve HTML/JavaScript
that would execute in a real visitor's browser, never leave the claim standing,
and exfiltrate nothing. If claiming is *not* allowed, stop at the confirmed
dangling record plus the can-i-take-over-xyz status and hand it to triage.

### Phase 4 — Report

One finding, one subdomain, impact-first. Evidence checklist in the
*Reporting evidence* section below.

## Where the money is with subjack

| Finding class | Reaches it via | Typical severity | Dupe risk |
|---|---|---|---|
| NS delegation takeover (dangling cloud DNS zone / expired NS domain) | `-ns` | Critical — control of the whole zone's records | Low–Medium — few tools check NS |
| Dangling CNAME → claimable SaaS (Pages/bucket/app) | default check + `-a` | High; Critical if the parent scopes cookies/OAuth/session to the subdomain | **Very high** — the most automated bug class in bounty |
| Expired SPF `include:` / MX takeover | `-mail` | Medium–High — spoofing or inbound mail interception | Low |
| Stale A record → reclaimable cloud IP | `-ar` | Medium–High — hard to prove reclaimability | Medium |
| Open AXFR zone transfer | `-axfr` | Medium — information disclosure (full record set) | Low |
| SRV record takeover | `-srv` | Low–Medium — depends on the service behind it | Very low |

Two severity levers worth stating explicitly in the report. First, **NS
beats CNAME**: taking over a delegation gives you authority over *every* record
in the zone, not one hostname, so it outranks a single dangling CNAME. Second,
a CNAME takeover's severity is set by *what the parent domain trusts the
subdomain for* — session cookies scoped to `*.example.com`, an OAuth redirect
allowlist, SSO, or password-reset/email links routed through the host turn a
Medium "defacement" into a High/Critical account-takeover chain. Research the
trust relationship before you pick a severity.

The dupe-risk column is the honest part. CNAME takeover is the single
most-scanned bug class in bug bounty; identical automation runs against popular
programs continuously, so a dangling CNAME on a long-lived asset is almost
always already reported. The unclaimed ones live on **fresh or decommissioned
assets**: a just-added subdomain, a service turned off last week, a recent
acquisition's DNS folded into scope. The less-automated classes (`-ns`,
`-mail`, `-axfr`) are where a careful hunter still finds first-reporter value.

## Command recipes

Low-volume defaults matter here for a reason most scanners don't have:
subjack's checks are DNS-query amplifiers. `-srv` adds roughly 28 DNS queries
*per domain*; `-axfr` brute-forces nameserver hostnames (extra lookups per
domain); `-ar` may need root for ICMP and probes third-party cloud IPs
(falling back to TCP 80/443). On a large list these multiply fast, and the
`8.8.8.8` fallback resolver will rate-limit or temporarily block you. Always
supply your own resolvers with `-r`, keep `-t` modest, and enable the
amplifying checks on *shortlists*, not the whole corpus.

```
# Baseline CNAME sweep — polite threads, real resolvers, HTTPS, JSON out
subjack -w scope.txt -a -ssl -t 20 -timeout 30 -r resolvers.txt -o cname.json
# -> candidates whose CNAME chain terminates at an unclaimed service are
#    written to cname.json; verify each against can-i-take-over-xyz.
```

```
# Pipe straight from enumeration (filter to scope first)
subfinder -d example.com -silent | subjack -a -ssl -r resolvers.txt -o live.json
# -> names arriving on stdin are resolved and checked; -a reaches the
#    CNAME-less dangling cases the default shortcut skips.
```

```
# Nameserver / delegation takeovers — the high-severity, low-dupe class
subjack -w scope.txt -ns -r resolvers.txt -o ns.json
# -> flags expired registrable NS domains and cloud-DNS zones answering
#    SERVFAIL/REFUSED to SOA probes (Route 53, Google Cloud DNS, Azure DNS,
#    DigitalOcean, Vultr, Linode).
```

```
# Mail-path takeovers — expired SPF include: and MX targets
subjack -w scope.txt -mail -r resolvers.txt -o mail.json
# -> names an expired, registrable domain in an SPF include: chain or MX
#    record; impact is spoofing / inbound interception, not host control.
```

```
# Zone transfers — single high-value asset list, not a mass run
subjack -w nameservers.txt -axfr -r resolvers.txt -o axfr.json
# -> ZONE TRANSFER lines name the vulnerable server and the record count
#    exposed. This is disclosure; frame it as such.
```

```
# Stale A records — needs root for the ICMP path; TCP 80/443 fallback otherwise
sudo subjack -w scope.txt -ar -r resolvers.txt -o stale.json
# -> STALE A RECORD lines. The README is explicit: verify manually, a dead
#    IP is not always reclaimable.
```

```
# Research signal, not a claim: flag dead CNAMEs even when the target
# domain is NOT currently registerable
subjack -w scope.txt -m -o deadrecords.json
# -> surfaces hygiene problems and future takeover candidates; these are
#    usually informational until the backing domain becomes claimable.
```

The flags above are the complete current set relevant to hunting: `-d`
(single domain), `-w` (wordlist), `-t` (threads, default 10), `-timeout`
(seconds, default 10), `-o` (output; `.json` extension → JSON), `-ssl` (force
HTTPS, may improve accuracy), `-a` (request every URL, recommended), `-m`
(flag dead records even when the domain isn't registerable), `-r` (resolver
list, falls back to `8.8.8.8`), `-ns`, `-ar`, `-axfr`, `-mail`, `-srv` (the
opt-in check classes), and `-v` (verbose).

## Pitfalls, noise and scope discipline

- **"Not found" ≠ claimable.** The fastest way to a rejected report is
  trusting the fingerprint over the provider's current behavior. Many services
  that were once takeoverable (AWS/CloudFront, modern Shopify, Heroku's
  verified apps) now require proof of domain ownership before you can attach a
  name. A dangling CNAME to a *Not vulnerable* entry in can-i-take-over-xyz is
  a non-issue — the README says it plainly: *always double-check results
  manually to rule out false positives.*
- **Claiming is a state change — treat it like one.** Registering the resource
  can interfere with the target's own recovery, and a claimed asset left
  standing is an open door. Only claim if the program allows it, serve benign
  proof only, and relinquish the moment you have the screenshot. Serving
  executable content to real visitors is attacking the users, not the program.
- **Scope drift from the enumerator.** Piping subfinder/amass straight in will
  scan wildcards, partner domains, and acquisitions. Filter to in-scope apexes
  in Phase 0 — an impeccable takeover on an out-of-scope host pays nothing and
  risks your standing on the program.
- **Query amplification and resolver blocking.** `-srv` (~28 queries/domain),
  `-axfr` (nameserver brute force), and `-ar` (ICMP/TCP probes) multiply load;
  a big list at `-t 100` plus the `8.8.8.8` fallback gets you rate-limited and
  produces false negatives from dropped queries. Supply `-r resolvers.txt`,
  keep threads modest, and run the amplifying checks on shortlists.
- **`-ar` needs root and probes third parties.** The ICMP path requires
  privilege; without it subjack falls back to TCP on 80/443. Either way you are
  sending probes to cloud-provider IPs — keep it low and verify reclaimability
  before claiming anything.
- **Fingerprints lag the live corpus.** Any bundled fingerprint set drifts
  behind can-i-take-over-xyz. Re-check the service's current status at report
  time, and use `-a` to counter the opposite failure (missing CNAME-less
  danglers). Stale fingerprints cause both false positives *and* false
  negatives.
- **Right class, wrong impact claim.** `-axfr` is information disclosure, not
  host control; `-mail` is spoofing/interception, not takeover of the web
  origin. Label each finding with the impact its class actually supports — a
  triager will downgrade an over-claimed report and remember it.

## Reporting evidence

Capture, per finding:

- **The dangling record and its chain.** `dig +trace` / `dig CNAME` output
  showing the full CNAME chain and where it terminates (NXDOMAIN or the
  provider's "no such site" response), plus the matching subjack output line.
- **The service and its claimability status.** The fingerprint subjack matched
  *and* the corresponding can-i-take-over-xyz entry (status + fingerprint
  string) proving the service is currently claimable — this is what separates a
  real finding from a stale-fingerprint false positive.
- **Proof of control (if claiming is permitted).** The resource you created on
  the provider (repo/bucket/app name), a screenshot or `curl` of your unique
  benign token served at a nonce path on the subdomain
  (`https://staging.example.com/.bug-bounty-poc-<random>.txt`), and a
  timestamp. State explicitly that you served only benign content, exfiltrated
  nothing, and relinquished the resource.
- **Class-specific artifacts.** NS: the `SOA` SERVFAIL/REFUSED responses and
  the dangling zone. Mail: the expired `include:`/MX domain plus RDAP/WHOIS
  showing it is registrable. AXFR: subjack's `ZONE TRANSFER` line with the
  nameserver and record count (do not dump the zone contents into the report —
  summarize and redact). Stale A: the dead IP and your reclaimability check.
- **Impact and trust relationship.** Name what the parent domain trusts the
  subdomain for (cookie scope, OAuth redirect allowlist, SSO, email links) so
  the triager can see why the severity is what you claimed.
- **Cleanup statement.** One line confirming you removed the claimed resource
  and left no attacker-controlled content behind.

## References

- subjack — official repository and README (flags, check classes, provider
  lists, manual-verification warnings): https://github.com/haccer/subjack
- EdOverflow, *Can I take over XYZ?* — canonical per-service takeover status
  and fingerprints (cited by subjack's README):
  https://github.com/EdOverflow/can-i-take-over-xyz
- indianajson, *Can I take over DNS?* — nameserver/delegation takeover
  reference (cited by subjack's README for the `-ns` class):
  https://github.com/indianajson/can-i-take-over-dns
- haccer/available — the RDAP-based registrability check subjack uses to
  confirm NXDOMAIN registration: https://github.com/haccer/available

*Research supplement added 2026-11-16, grounded in the haccer/subjack README
(github.com/haccer/subjack) and the EdOverflow can-i-take-over-xyz /
indianajson can-i-take-over-dns corpora cited therein (model: claude-opus-4-8).*
