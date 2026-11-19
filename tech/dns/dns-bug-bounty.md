---
technology: "DNS"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
version: "1.0"
last-refresh: "2026-11-19"
status: upstream-only
provenance: "web-researched 2026-11-19 (model claude-opus-4-8). Tool flags verified against ProjectDiscovery docs (dnsx -wd/-wt, shuffledns -mode/-r, alterx -enrich/-ac, subfinder -all/-rL), dnsrecon man page (-t axfr/-a/-t brt), ISC/delv DNSSEC validation guides, EdOverflow/can-i-take-over-xyz fingerprint model, and the 2026 'Borrowed Trust' NS-delegation campaign (Cyble/DEV/Akamai). External-surface angle complements the role-fingerprint overview [[dns]] and the internal-server variant [[dns-bind]]."
source: "ProjectDiscovery dnsx/shuffledns/alterx/subfinder usage docs (accessed 2026-11-19); dnsrecon man page (Debian/Kali); OWASP WSTG subdomain-takeover test; EdOverflow/can-i-take-over-xyz; AWS Route53 dangling-delegation docs; Cyble/DEV/Akamai 'Borrowed Trust' Azure DNS takeover reporting (2026); oneuptime/ISC DNSSEC-with-dig-and-delv guides. CVE/campaign facts spot-verified; tool flags are version-sensitive — confirm with `-h` on the installed build."
---

# DNS bug bounty methodology

Companion pages: [[dns|DNS overview]] routes by server role (resolver / authoritative / AD-integrated); [[dns-bind|BIND variant]] owns internal named.conf/TSIG/zone-file chains. **This page is the external-surface angle only**: DNS as the reconnaissance substrate and as a finding class in its own right — zone exposure, subdomain discovery, dangling-record takeover, rebinding, and information leakage, all from outside the perimeter. Tool pages live in the separate tools vault; this document names tools in prose and maps the workflow onto them.

**In scope here:** everything you can learn and prove against a target's *public* DNS — authoritative nameservers, public zone data, delegation chains, records pointing at third-party services. **Out of scope / elsewhere:** cache poisoning and resolver-control takeover (resolver personality → [[dns]]), ADIDNS/DnsAdmins and dynamic-update injection (internal → [[dns]]/[[dns-bind]]/[[ldap]]).

**Targets below are always `example.com` / `*.example.com` / `198.51.100.0/24`.** Authorized-program discipline is baked in: **AXFR and active resolution run ONLY against nameservers authoritative for a zone that is in scope**; enumeration is passive-first and low-volume; every claim is evidence-first, and takeover is never demonstrated by seizing production traffic.

---

## Where the money is (triage priority)

| # | Class | Lives in | Pre-auth? | Evidence bar | Typical severity |
|---|---|---|---|---|---|
| 1 | Dangling **NS** delegation takeover | parent zone's NS set → dead cloud zone | yes | you control the delegated zone's answers | Critical |
| 2 | Dangling **CNAME/A** subdomain takeover | victim zone → unclaimed 3rd-party tenant | yes | your content served on victim hostname | High–Critical |
| 3 | Zone transfer (AXFR) succeeds | authoritative NS with broad transfer ACL | yes | full zone dumped in one query | High (full namespace disclosure) |
| 4 | DNS rebinding → SSRF filter bypass | app that resolves, validates, then re-connects | varies | internal resource reached via rebind | High (chain-dependent) |
| 5 | Internal-IP leakage in public zone | `A`/`AAAA` records → RFC1918 in a public zone | yes | public record resolving to private space | Low–Medium (recon→chain) |
| 6 | Subdomain over-exposure | CT logs + brute reveal internal-named hosts | yes | reachable staging/admin/VPN host | Info–Medium (feeds everything) |
| 7 | DNSSEC absent / broken chain | zone unsigned or validation fails | yes | no AD flag / `delv` fails | Info (spoofing-enabler note) |
| 8 | Missing egress/exfil detection | tunneling reaches the resolver unflagged | n/a | blue-team gap, not a bounty | Info (assessment finding) |

Rows 1–2 are where single findings pay the most and where DNS owns the external surface outright; rows 3 and 5–7 are recon that *becomes* a finding when it reaches something sensitive; row 4 is a primitive you carry into a web target. Work 0→7 on a fresh scope.

---

## Phase 0 — Fix the authoritative set and the scope boundary

Goal: before any active query, know exactly **which nameservers you are allowed to transfer/brute against**. The transfer ACL and the brute target must be a server authoritative for an in-scope zone — nothing else.

```bash
dig +short NS example.com                    # the authoritative set for the apex
# -> ns1.example.com.
#    ns2.exampledns.net.          <- note: a 3rd-party/cloud NS is a takeover lead (Phase 4)

dig +short SOA example.com                   # primary master + serial (serial moves = dynamic zone)
dig example.com ANY +noall +answer @ns1.example.com   # MX/TXT/SPF/DMARC in one shot
```

Three things come out of Phase 0: the **NS set** (each server has its own ACL — test every one, not just the first), the **SOA primary** (the master is the most likely to accept AXFR and to carry a dynamic zone), and the **delegation shape** (an NS pointing at `*.azure-dns.com`, `awsdns`, or any third-party provider is a Phase 4 lead the moment one of those zones is unclaimed). Fingerprint the server role per [[dns]] (`dig @ns CH TXT version.bind`) — but on the external surface you mostly care that the box is *authoritative for your in-scope zone*, which the NS record already told you.

---

## Phase 1 — Zone transfer (AXFR): the classic, with expected outputs

AXFR is a *query*, not an exploit: any secondary-capable master whose `allow-transfer` ACL includes you hands over the entire zone over TCP/53. It is the single highest-value first move and it is non-destructive. Run it against **every NS from Phase 0**, for **every in-scope zone you know** (apex and any delegated subzones).

```bash
dig AXFR example.com @ns1.example.com        # the request; TCP/53
```

**Refused (the common, healthy case)** — the ACL blocks you:

```
; <<>> DiG 9.18.x <<>> AXFR example.com @ns1.example.com
;; global options: +cmd
; Transfer failed.
```

A bare `; Transfer failed.` (often with `REFUSED` / `NOTAUTH` upstream, or a TCP timeout when 53/tcp is filtered) means move to the next NS — **ACLs differ per server**, and a second nameserver forgotten in a hardening pass is the whole finding.

**Accepted (the finding)** — the zone falls out, bracketed by its SOA:

```
example.com.        3600  IN  SOA   ns1.example.com. hostmaster.example.com. 2026111901 ...
example.com.        3600  IN  NS    ns1.example.com.
dev.example.com.    3600  IN  A     198.51.100.23
vpn.example.com.    3600  IN  A     198.51.100.44
_dmarc.example.com. 3600  IN  TXT   "v=DMARC1; p=none; ..."
example.com.        3600  IN  SOA   ns1.example.com. ...        <- closing SOA = full transfer
```

`dnsrecon` automates the per-NS sweep and parses the result cleanly:

```bash
dnsrecon -d example.com -t axfr           # tries AXFR against every authoritative NS, per-server verdict
dnsrecon -d example.com -a                # AXFR + standard enumeration in one pass
```

What a successful AXFR gives you that nothing else does: the **complete internal namespace** in one query — every host the operator ever named, including the ones never meant to be public (`backup`, `jenkins-internal`, `old-vpn`), plus `TXT` records that routinely leak verification tokens, mail config, and sometimes infra notes. If AXFR refuses everywhere, you still collected `NS/SOA/MX/TXT` in Phase 0; that is the baseline. **Evidence:** capture the full `dig`/`dnsrecon` output including the server you hit and the closing SOA — the dump proves the ACL misconfiguration without you touching another service. Report impact as *namespace disclosure* (the attack surface you now have a map of), and name the fix: scope `allow-transfer` to the secondaries' IPs (and TSIG-gate it) rather than an open or broad-CIDR ACL.

---

## Phase 2 — Wildcard detection: pre-filter before you brute

Goal: decide whether the zone has a `*` record **before** any brute-force resolution, so the tool does not report thousands of phantom hits. A wildcard answers for *every* otherwise-nonexistent name; without this gate, every candidate "resolves" and your subdomain list is fiction.

The method is a controlled probe: resolve a couple of **guaranteed-nonexistent random labels** and see whether they answer.

```bash
# two 24-char random labels; if both resolve (to the same IP), a wildcard exists
for i in 1 2; do dig +short "$(head -c16 /dev/urandom | base32 | tr -d = | tr A-Z a-z).example.com"; done
# -> 198.51.100.80      (both answer, same A)  = WILDCARD present
# -> (empty)            (NXDOMAIN)             = no wildcard, brute results are real
```

This is exactly what the mature tools do internally, so prefer letting them handle it at scale rather than hand-filtering:

- **`dnsx`** does wildcard elimination with `-wd` (the domain is mandatory for the filter) and a tunable `-wt` threshold (default 5). Feed it a candidate list and it strips the phantoms: `dnsx -l candidates.txt -wd example.com -o resolved.txt`. Note the filter can't be combined with arbitrary record-type flags — run wildcard-filtered A resolution as its own pass.
- **`shuffledns`** (a `massdns` wrapper — `massdns` must be installed) counts how often an IP recurs and, past a threshold, probes random names to confirm the wildcard before emitting results, so its brute/resolve output is already wildcard-clean.

The nuance worth internalizing: wildcard detection is **per-label-depth**. A wildcard at `*.example.com` does not imply one at `*.dev.example.com`; tools re-test at each level because a single apex wildcard otherwise poisons every deeper brute. When a wildcard *is* present, pivot — brute-force is dead for that level, so lean on passive sources (Phase 3) and on content-level differentiation (does the phantom IP actually serve the app, or a parked page?).

---

## Phase 3 — Subdomain enumeration: DNS as one source among many

Goal: the fullest possible host inventory, with DNS brute as **one** source feeding a resolver, not the whole method. Certificate Transparency logs and passive aggregators find names that were never brute-guessable; DNS brute finds names that were never issued a cert. You want both, de-duplicated, then resolved through a clean resolver set.

**Passive first (no packets at the target's auth servers):**

```bash
subfinder -d example.com -all -o passive.txt        # -all = every source (slower, fuller); CT logs included
```

`subfinder` aggregates CT logs and dozens of passive sources; `-all` turns them all on. This is zero-touch on the target and should always precede brute.

**Active brute (resolver hygiene is the whole game):**

```bash
# bruteforce mode: wordlist × domain, wildcard-handled, against YOUR resolver list
shuffledns -d example.com -w wordlist.txt -r resolvers.txt -mode bruteforce -o brute.txt
```

The `-r resolvers.txt` file is not a detail — it is the correctness of the entire result set. **Resolver hygiene rules:**

- **Never brute through the target's own authoritative NS** (it is low-volume-only surface and it taints your data) or through a single public resolver (rate-limited, and it caches poisoned answers). Use a maintained list of **fast, validated public resolvers**, and periodically re-validate it (dead or lying resolvers manufacture false positives). ProjectDiscovery's and `trickest`'s resolver lists are the common starting sets; the discipline is to keep a *trusted* subset for final verification and a larger set for bulk.
- **Resolve passive + brute together through the trusted set** to confirm liveness: `shuffledns -d example.com -list all-candidates.txt -r trusted-resolvers.txt -mode resolve`, or `dnsx -l all-candidates.txt -wd example.com`.

**Permutation (second-order names):** once you have real hosts, `alterx` generates pattern-based permutations (`dev-api`, `api-staging`, `api2`) from the names you already found, and pipes straight into a resolver — keeping the output small enough to resolve cheaply:

```bash
subfinder -d example.com -all | alterx -enrich | dnsx -wd example.com -o final.txt
# -enrich mines words from the input to fill the permutation patterns; -ac supplies a custom pattern config
```

**Evidence/impact:** enumeration is rarely a standalone bounty — it is the map. A finding appears when a discovered host *is itself* sensitive or reachable (an exposed `staging.`, `jenkins.`, `vpn-test.`, an internal admin panel on a public IP). Report that host, not the wordlist. The enumeration output also feeds Phase 4 directly: every CNAME you turned up is a takeover candidate.

---

## Phase 4 — Dangling-record takeover: the class DNS owns outright

Goal: find records that still alias a third-party resource whose tenancy lapsed, and **prove control** of what they point at — nothing less. This is the external-surface class with the highest single-finding payout, and the one most often reported *wrong* (a dangling CNAME is a lead, not a finding).

### 4.1 CNAME/A to a claimed-service pattern

The defect lives in the victim's own zone: a `CNAME` (or `A`) still pointing at `*.cloudfront.net`, `*.herokuapp.com`, `*.myshopify.com`-class targets after the subscription/app/bucket was dropped. DNS keeps aliasing; the origin claim is gone; whoever re-registers the name at the provider serves content on the victim's hostname.

```bash
# sweep the enumeration output for aliases into third-party service domains
while read d; do printf '%s %s\n' "$d" "$(dig +short CNAME "$d")"; done < final.txt | grep -Ei \
  'cloudfront|heroku|azurewebsites|github\.io|myshopify|fastly|s3[.-]|trafficmanager|elasticbeanstalk'
```

Each hit is a **candidate** that must clear two gates in order:

1. **Is this service family claimable at all?** The arbiter is the maintained **can-i-take-over-xyz** matrix (EdOverflow; machine-refreshed `fingerprints.json`). Entries carry a status (Vulnerable / Not Vulnerable / Edge case) and a fingerprint that is *either* a response regex *or* an NXDOMAIN indicator. Several resonant error pages belong to services **nobody can re-register** — a claim attempt there is a report that dies in triage. Check the matrix before spending effort.
2. **Does the fingerprint actually match right now?** Fetch the dangling name over HTTP(S) and match the provider's no-tenant signature — CloudFront's `ERROR: The request could not be satisfied`, Heroku's `No such app`, GitHub Pages' `There isn't a GitHub Pages site here`, Shopify's unclaimed-store template. For **NXDOMAIN-class** services the signal is at the DNS layer instead: the CNAME target itself returns NXDOMAIN (the provider resource is gone), which is what makes the label registrable.

```bash
dig +short CNAME app.example.com          # -> deleted-bucket.s3.amazonaws.com.
dig +short app.example.com                # -> NXDOMAIN on the target = registrable (NXDOMAIN-class)
curl -s https://app.example.com/ | head   # -> provider no-tenant page = regex-class fingerprint
```

`subzy`, `subjack`, and the `nuclei` takeover templates automate the sweep-plus-fingerprint over large lists (`subzy` matches live HTTP responses, so it confirms *claimable pages*, not merely dangling CNAMEs). Treat their output as a candidate queue, then verify by hand.

### 4.2 Dangling NS delegation takeover (the 2026 escalation)

A level up from CNAME: when a **parent zone delegates a subzone to a cloud DNS provider** (`sub.example.com NS ns1-xx.azure-dns.com`) and the cloud-side zone is later deleted while the delegation survives, whoever re-creates a zone of that exact name in the provider **owns resolution for the entire subtree** — every record under it, not one hostname. The 2026 "Borrowed Trust" campaign hijacked 163 organizations this way, the large majority through **Azure DNS** zone re-creation after subscriptions lapsed; AWS documents that Route 53 only auto-protects the *hosted-zone-deletion* case and that other dangling-delegation scenarios remain exploitable.

```bash
dig +short NS sub.example.com             # -> ns1-07.azure-dns.com.  (delegated to a cloud provider)
dig SOA sub.example.com @ns1-07.azure-dns.com   # SERVFAIL / no SOA from the provider's NS
# -> provider is authoritative-by-delegation but has NO zone = the zone name is re-creatable
```

The tell is a delegation to a provider whose nameservers **do not actually serve the zone** (SERVFAIL, REFUSED, or "no such zone"). Enumerate NS records across the whole discovered namespace, not just the apex. **Do not** complete the hijack on a live target — the evidence standard (4.3) is satisfied by demonstrating the zone name is unclaimed in a controlled way (e.g., provider API shows the name available, or a benign zone you create in *your own* account begins receiving the delegated queries), coordinated with the program.

### 4.3 The evidence standard: control-proof, not dangling-proof

A triager rejects "this CNAME dangles." They accept **"I serve content under the victim's hostname / I answer the victim's delegated subtree."** The proof is control, shown minimally and reversibly:

- Claim the resource **in your own account**, attach the victim hostname as a custom domain (or create the benign provider zone), and serve a **unique innocuous token** at a single path — `/<bounty-handle>-poc.txt` — never attacker content, never real-looking login pages.
- Capture: the dangling record (`dig`), the provider no-tenant fingerprint *before* claiming, and your token served *after*. That triple is unambiguous.
- **Impact framing** writes itself: attacker-controlled content under the corporate hostname (cookie scope included → session theft), credible phishing from a trusted domain, and — for wildcard or NS-level takeover — the ability to inherit the provider-issued wildcard certificate and answer for *every* name in the subtree. For cert-inheritance mechanics and the wildcard-cert angle, cross-ref [[ssl-tls]]. Cloud-side claim workflows differ per provider — [[aws]] / [[azure]] / [[gcp]] own the account-level steps. Then **release the resource** and tell the program you have done so.

---

## Phase 5 — DNS rebinding as an SSRF-filter-bypass primitive (analysis framing)

Goal: recognize when a server-side fetcher's SSRF filter is defeatable by rebinding, and frame it correctly in a report. This is a *primitive you bring to a web target*, not a standalone DNS bug — the defect is in the application's **check-then-connect** gap, DNS is the delivery mechanism.

The precondition is a specific code shape: the app resolves a user-supplied hostname, **validates the resolved IP** (rejects RFC1918/link-local), and then **re-resolves and connects without pinning** the address it validated. A record served with **TTL 0** (or 1s) makes the two lookups return different answers: the first (public IP) passes the filter; the second (after the TTL expires) returns `127.0.0.1` / `169.254.169.254` / an internal host, and the connection lands there.

```
# attacker-controlled zone, rebinding record:
rebind.attacker.example   0  IN  A  203.0.113.10     # 1st lookup: public, passes the filter
#                        then flips to:
rebind.attacker.example   0  IN  A  169.254.169.254  # 2nd lookup: cloud metadata, connection target
```

Whether it fires depends on **resolver behavior**, which is the analysis you must do rather than assume:

- A resolver honoring the authoritative TTL re-queries each time (TTL 0 → no caching) and is rebindable; a resolver enforcing a **minimum cache TTL** (BIND/dnsmasq hardening) blunts it.
- The attack needs the filter and the connection to each perform their **own** lookup — a stack that resolves once and connects to that exact IP is immune.
- Cloud metadata with **IMDSv2** resists rebinding because the token step needs a header the no-cors fetch can't set; `169.254.169.254` on IMDSv1 does not.

`Singularity` (NCC Group) is the reference framework: it serves the rebinding zone, uses an index-token payload to detect *when* the flip has taken effect, and is the standard way to demonstrate the bypass under authorization. **Evidence:** show the SSRF filter accepting your hostname, then the internal response body returned after rebind (a metadata document, an internal-only page). Report it as an SSRF filter bypass with the root-cause fix: **pin the validated IP for the actual connection**, or resolve-and-check at connect time against the address you dial.

---

## Phase 6 — Information leakage in public DNS

Goal: the low-noise findings that live in the zone data itself. Individually minor; each is a perimeter-misconfiguration signal and a chain input.

### 6.1 Internal (RFC1918) addresses in a public zone

A public `A`/`AAAA` record resolving into `10.0.0.0/8`, `172.16.0.0/12`, or `192.168.0.0/16` is a perimeter leak: it publishes internal topology (segment layout, host naming, often the real address of a service only meant to be reachable over VPN), and it is a ready-made SSRF/pivot target the moment you are inside.

```bash
# flag public records pointing into private space across the resolved inventory
dnsx -l final.txt -a -resp -o resolved-with-ips.txt
grep -E '(\b10\.|172\.(1[6-9]|2[0-9]|3[01])\.|192\.168\.)' resolved-with-ips.txt
# -> intranet.example.com [10.2.4.19]      = internal A record in a public zone
```

Report as information disclosure / misconfiguration; severity rises when the leaked host is clearly sensitive (`intranet.`, `vcenter.`, `db-`) or when it hands an SSRF chain its internal target directly.

### 6.2 DNSSEC status as an exposure signal

An unsigned zone (or a broken chain) means answers for the domain can be spoofed on-path without detection — a spoofing *enabler*, usually Info-level on a bounty but a genuine hardening finding in an assessment. Check it, don't assume it:

```bash
dig +dnssec example.com                # look for RRSIG records alongside answers, and the AD flag in the header
#   ;; flags: qr rd ra ad;             <- 'ad' = a validating resolver verified the chain
dig +short DNSKEY example.com          # empty = zone is unsigned
delv example.com                       # authoritative verdict: "; fully validated" vs "; no valid signature"
```

Query a validating resolver (1.1.1.1 / 8.8.8.8 / 9.9.9.9) when reading the `AD` flag — a non-validating resolver never sets it, so absence of `AD` from the wrong resolver is not proof of an unsigned zone. A published `DNSKEY` with no matching `DS` at the parent is a **broken chain** (signed but unvalidatable) — worth a line because it is often mistaken for "signed and fine." Deeper DNSSEC/TLS trust-chain interplay: [[ssl-tls]].

### 6.3 Mail-authentication records (SPF/DMARC/DKIM)

TXT records are public policy you can read without touching mail: `v=spf1 ... ~all` vs `-all`, `_dmarc` with `p=none`, missing DKIM selectors. A permissive SPF or `p=none` DMARC is a spoofing-posture finding and often a chain input for phishing from the domain (pairs naturally with a Phase 4 takeover). The record parsing belongs to [[smtp]]; note the DNS exposure here and hand off.

---

## Phase 7 — Tunneling / exfil detectability (blue-red framing)

Goal: in a red-team or purple engagement, characterize whether DNS exfiltration/C2 would be *seen*. This is an assessment observation, not a bounty class — the deliverable is "your resolver logs would/would not have caught this," framed for the blue team.

DNS tunneling (`iodine`, `dnscat2`-class) rides **recursive resolvers**, encoding data into query labels and answers. The detectable signatures, with the honest caveat that thresholds are baselines to tune, not universal constants:

- **Label entropy** — encoded payloads push per-character Shannon entropy toward ~3.5–4.0 bits, well above human-chosen names.
- **Query length** — tunnels maximize payload, driving labels toward the 255-char limit; long subdomains from one host are a flag.
- **Record-type ratio** — a single client whose `TXT` (or `MX`/`CNAME`) share spikes far above its baseline; `dnscat2` rotates `MX`/`TXT`/`CNAME` by default.
- **NXDOMAIN rate** from one host against one second-level domain — a *secondary* signal (DGA malware produces the same shape), so correlate with entropy/length rather than alerting alone.
- **DoH/DoT blind spot** — encrypted resolution hides the plaintext labels entirely; note it as a monitoring gap, since it defeats label-based detection.

Frame the finding to the customer as detection coverage: which of the above their tooling baselines, and whether egress DNS is forced through a logged resolver at all. Red-side, this tells you *how loud* a DNS channel would be before you ever consider using one under the ROE.

---

## Reporting and evidence standards

The report is the product; DNS findings live or die on the proof being **control, not inference**.

1. **Title — impact first.** "Subdomain takeover of `app.example.com` via dangling CloudFront CNAME — attacker serves content under the corporate hostname" beats "dangling DNS record."
2. **Preconditions.** What you needed (nothing but public DNS, in most of this document) and the exact in-scope target.
3. **PoC — copy-pasteable, one step per line, expected vs actual.** For AXFR: the `dig AXFR … @ns` command, the server, and the closing SOA. For takeover: the `dig` showing the dangling target, the no-tenant fingerprint *before*, your unique token served *after*. For rebinding: filter-accept then internal-response-after-rebind.
4. **Evidence hygiene.** Serve only a benign token, never real-looking content; read only the zone data the proof needs (an AXFR dump is evidence — don't go on to probe every host it revealed without separate authorization); **release claimed resources** and say so; clean up test zones.
5. **Remediation — root cause.** Scope `allow-transfer` to secondary IPs + TSIG (AXFR); remove the record or reclaim the resource, then audit *all* delegations/CNAMEs (takeover); pin the validated IP at connect time (rebinding); sign the zone and publish the `DS` (DNSSEC).

Severity rules of thumb: NS-delegation or wildcard takeover — Critical; single-host CNAME takeover — High–Critical by what the hostname gates; AXFR of a production zone — High (namespace disclosure); rebinding-to-internal — High in a chain; internal-IP leak / SPF / DNSSEC — Low–Info unless they directly enable a demonstrated chain. Map to the program's rubric.

---

## CTF notes

DNS CTF challenges compress this methodology into a few reliable moves:

- **AXFR is the first thing to try** on any CTF nameserver — `dig AXFR <zone> @<box>` frequently dumps a flag host, a hidden vhost, or a `TXT` record carrying the flag directly. Boxes deliberately leave the ACL open.
- **`TXT` records hide flags and hints** — always `dig TXT` the apex and every discovered name; base64/hex in a `TXT` is a near-universal challenge pattern.
- **Hidden vhosts via enumerated names** — a subdomain you brute/transfer out often maps to a vhost the web challenge gates on `Host:`; resolve it, then set the `Host` header.
- **Wildcard trolls** — a `*` record that answers everything is a deliberate time-sink; run the random-label probe (Phase 2) first so you don't chase phantoms.
- **Takeover sims** — a dangling CNAME to a provider you *can* register in the challenge environment is a set-piece; the flag drops once your content serves on the victim name.
- **PTR sweeps** — once you know the challenge subnet, reverse-resolve it (`dig -x`) for named hosts the forward zone hid.
- **DNSSEC/NSEC walking** — an NSEC-signed zone can be *walked* (NSEC records chain the sorted names), enumerating every name without brute; `dig +dnssec` and follow the `NSEC` chain, or let `dnsrecon -t zonewalk` do it.

CTF DNS is "read the zone the author left slightly too open"; production DNS is "prove you control what a forgotten record points at." Same tools, opposite discipline.

---

## References

- ProjectDiscovery dnsx — usage / wildcard filtering (`-wd`, `-wt`): https://docs.projectdiscovery.io/opensource/dnsx/usage
- ProjectDiscovery shuffledns — modes, resolvers, massdns wrapper: https://github.com/projectdiscovery/shuffledns
- ProjectDiscovery alterx — pattern permutation (`-enrich`, `-ac`): https://docs.projectdiscovery.io/opensource/alterx/running
- ProjectDiscovery subfinder — passive sources (`-all`, `-rL`): https://github.com/projectdiscovery/subfinder
- dnsrecon man page (AXFR `-t axfr`/`-a`, brute `-t brt`, zonewalk): https://www.kali.org/tools/dnsrecon
- EdOverflow / can-i-take-over-xyz — service claimability + fingerprints: https://github.com/EdOverflow/can-i-take-over-xyz
- OWASP WSTG — Test for Subdomain Takeover: https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/10-Test_for_Subdomain_Takeover
- AWS Route 53 — protection from dangling delegation records: https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/protection-from-dangling-dns.html
- "Borrowed Trust" Azure DNS NS-delegation takeover campaign (2026) — Cyble: https://cyble.com/blog/borrowed-trust-cloud-dns-takeover-thai-gambling-seo-poisoning/
- "Borrowed Trust" — 163 brands via abandoned delegations: https://dev.to/dnsassistant/163-brands-hijacked-through-abandoned-dns-delegations-inside-the-borrowed-trust-campaign-4je8
- Akamai — dangling DNS as overlooked attack surface: https://www.akamai.com/blog/security/dangling-dns-most-overlooked-attack-surface-ai-era
- NCC Group Singularity of Origin (DNS rebinding framework): https://github.com/nccgroup/singularity
- Validate DNSSEC signatures with dig and delv: https://oneuptime.com/blog/post/2026-01-15-validate-dnssec-signatures-dig-delv/view
- DNS tunneling detection for SOC analysts (entropy/length/NXDOMAIN signals): https://cyberdefenders.org/blog/dns-tunneling-detection/

*Researched and written 2026-11-19 (model claude-opus-4-8). Tool flags verified against ProjectDiscovery, dnsrecon, and ISC/delv documentation; the dangling-NS-delegation technique verified against 2026 "Borrowed Trust" reporting and AWS Route 53 docs; command outputs are representative. Tool flags are version-sensitive — confirm with `-h` on the installed build. Companion: [[dns]] (role router), [[dns-bind]] (internal variant).*
