---
name: "nmap"
categories:
  - scanner
version: "git (2026-11-05)"
homepage: "https://github.com/nmap/nmap"
origin: "github"
recipe: "upstream"
packaged-by: [blackarch, arch, kali, nixpkgs]
build-system: c
meta-checked: "2026-11-09"
github-repo: "nmap/nmap"
github-stars: 13959
github-pushed: "2026-11-09"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 541
gh-activity-checked: "2026-11-09"
worth: 9.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
license: see repository
blackarch-builddate: "2018-11-04"
arch-builddate: "2026-10-28"
github-issues: 726
---

# nmap

> The network mapper — host discovery, port scanning, service/version detection, and scriptable service enumeration in one tool.

## What it's for

Finding out what is reachable on a network and what software answers behind each open port. nmap discovers live hosts, sweeps TCP or UDP port ranges, probes open ports to name the service and version, guesses the target operating system, and can run Lua scripts (NSE) against services for deeper enumeration or vulnerability checks. It is the standard opening move of network reconnaissance: its output — open ports, versions, hostnames, certificates, script findings — decides everything examined next. It maps attack surface; it does not exploit or hold access by itself.

## Typical use cases

### Quick scan of the top 1000 ports with scripts and version detection

The usual first look at a host: default port set, default NSE scripts, version probing.

```
nmap -sC -sV target.example.com
```

Outcome: prints a `PORT STATE SERVICE VERSION` table for every open port plus default-script output (HTTP titles, SSL certificate subjects, SMB details, banners), ending with `Nmap done: 1 IP address (1 host up) scanned in ...`. The service versions listed here drive what gets investigated next.

### Fast full-port sweep feeding a targeted deep scan

Split the job: find all open TCP ports quickly, then spend script and version probes only on those.

```
ports=$(nmap -p- --min-rate=1000 -T4 198.51.100.10 | grep '^[0-9]' | cut -d '/' -f 1 | tr '\n' ',' | sed s/,$//)
nmap -p $ports -sC -sV 198.51.100.10
```

Outcome: the first scan covers all 65535 TCP ports at no less than 1000 packets per second and leaves a comma-separated open-port list in `$ports` (e.g. `22,80,8080`); the second scan reports service versions and script results for exactly those ports. The standard workflow when the top-1000 scan looks incomplete.

### Full port range in a single pass

One scan instead of a two-stage split when a single report is wanted.

```
nmap -p- --min-rate=1000 -sC -sV 198.51.100.10
```

Outcome: version detection and default scripts across all 65535 TCP ports in one report; slower overall than splitting the work, but nothing gets missed between passes. Add `-Pn` when the host drops discovery probes.

### Aggressive scan with OS detection

One flag that enables OS detection, version scanning, script scanning, and traceroute.

```
nmap -A -v target.example.com
```

Outcome: the report gains an OS guesses block (`Running: Linux 5.X`, `Aggressive OS guesses: ...`) and a traceroute table on top of the version and script output; `-v` shows discovered ports live while scanning. For a bounded one-shot deep look, combine with a port selection: `nmap -Pn -A --top-ports 3000 198.51.100.10`.

### Quick UDP sweep of the most common ports

UDP scanning is slow per port, so start with the statistically likely ones.

```
sudo nmap -sU --top-ports 20 -sV 198.51.100.10
```

Outcome: scans only the 20 most commonly open UDP ports (DNS 53, SNMP 161, TFTP 69, and so on) with version probes. Root is required for the raw packets. Services like SNMP or TFTP that never show in a TCP scan surface here within minutes instead of the hours a full UDP range takes.

### Full UDP port sweep

Cover the whole UDP range when the quick sweep finds nothing or more is suspected.

```
sudo nmap -sU -p- --min-rate=1000 198.51.100.10
```

Outcome: all 65535 UDP ports with the transmit floor raised to keep the run tolerable; still far slower than TCP. `--open` suppresses the wall of `closed` lines so only responsive ports print, and `--min-parallelism` (e.g. `--min-parallelism=512`) pushes more probes in flight for stubborn targets. TCP and UDP can be combined in one scan with an explicit range: `-p u:0-65535,t:0-65535`.

### Ping sweep across a subnet

Discover which addresses are live before spending port-scan time on any of them.

```
nmap -sn 198.51.100.0/24
```

Outcome: host discovery only — one `Nmap scan report for ... Host is up` line per responding address, no port table. Drop `-sn` to follow every responder with the default top-1000 scan, or add `--min-rate 1000` to hurry large sweeps. A statically linked build of the binary can be copied to a pivot host and run the same way against internal subnets.

### Re-scan a specific port list

Concentrate scripts and version probes on ports that are already known to matter.

```
nmap -p 22,80,443,8080 -sC -sV 198.51.100.10
```

Outcome: full script and version output restricted to the listed ports, seconds instead of minutes. The same shape checks a service reachable only through an SSH port forward: `nmap -Pn -sC -sV -p 8111 127.0.0.1`.

### Run individual NSE scripts

Target one script — or a wildcard family of scripts — at the port it applies to.

```
nmap -Pn -p 389 --script ldap-rootdse.nse 198.51.100.10
```

Outcome: the named Lua script runs against the matching port and prints its findings as a script-results block under the port table (`-sC` is exactly `--script=default`). Scripts accept comma-separated names, wildcards (`--script nfs*` against `-p 111,2049`), and categories (auth, vuln, banner) from `/usr/share/nmap/scripts`.

### Scan through a SOCKS proxy

nmap's raw SYN packets cannot traverse a proxy, so force the connect scan.

```
proxychains nmap -sT -Pn -n -p 135,445,5985 198.51.100.10
```

Outcome: `-sT` uses ordinary TCP connects (which proxychains can wrap), `-Pn` skips discovery that would fail through the tunnel, and `-n` avoids DNS leaks; the chosen ports report open/filtered as seen from the proxy's exit. The standard way to enumerate hosts reachable only through an SSH dynamic forward (`ssh -D 1080`).

### Scan an IPv6 target

Point the whole toolkit at an IPv6 address.

```
nmap -6 -p- --min-rate=1000 -T4 2001:db8::10
```

Outcome: `-6` scans the given IPv6 address (SYN scan by default); every other flag behaves as in IPv4 scans. Output is the same per-port table with the IPv6 address in the report header.

### Save results in every output format

Keep the scan for later diffing, grepping, or tool import.

```
nmap -sC -sV -oA scans/target target.example.com
```

Outcome: writes `scans/target.nmap` (the normal readable report), `scans/target.gnmap` (one grepable line per host — `grep open scans/target.gnmap` lists ports from a shell), and `scans/target.xml` (machine-readable for downstream tooling). `-oN`, `-oX`, or `-oG` write just one of those formats to a named file.

### Probe past discovery-blocking defenses

When everything reads filtered or the host seems dead, stop trusting ping and shape the probes.

```
nmap -Pn -f --source-port 53 198.51.100.10
```

Outcome: `-Pn` skips host discovery and scans every port regardless (the single most common fix for all-filtered results), `-f` fragments the probe packets, and `--source-port 53` (short form `-g`) sources them from the DNS port so permissive stateful rules pass them. If specific ports then answer, re-run the deep scan against just those.

### Force probe-based discovery with --disable-arp-ping

Stop the implicit ARP shortcut from deciding a host is up on bridged or VPN networks.

```
ports=$(nmap --open 198.51.100.10 | grep open | cut -d ' ' -f 1 | cut -d '/' -f 1 | paste -sd,)
nmap 198.51.100.10 -p $ports -sV -sC -Pn --disable-arp-ping
```

Outcome: the first pass prints only open ports (`--open`), which the pipeline collapses into a comma list; the second pass disables nmap's automatic ARP/ND discovery of local-network targets so liveness comes from the probe packets themselves — on VPN or bridged segments where ARP always answers, this prevents a host that drops everything from masquerading as up with all ports filtered.

### Select NSE scripts with boolean expressions

```
nmap --script "safe or smb-enum-*" -p 445 198.51.100.10
nmap --script "snmp* and not snmp-brute" -sU -p 161 198.51.100.10
```

Outcome: `--script` accepts the `and`, `or` and `not` operators over categories, script names and `*` wildcards, with parentheses for grouping — the second line runs every SNMP script except the brute-forcer the wildcard would otherwise drag in. Quote the expression so the shell passes the spaces through. A name inside an expression can be a category, a script from `script.db`, or `all`.

### Look up scripts before running them

```
nmap --script-help "(default or version) and *smb*"
```

Outcome: prints the name, categories and full description of every script matching the expression — exactly the grammar `--script` itself accepts — without sending a packet. The quick way to review what `-sC` would do to a class of hosts, or to find the exact name of the script worth targeting.

### Brute-force service logins with NSE scripts

```
nmap --script smb-brute -p 445 198.51.100.10
nmap --script ldap-brute --script-args userdb=users.txt,passdb=passwords.txt,unpwdb.timelimit=30m -p 389 198.51.100.10
```

Outcome: the `brute`-category scripts (`smb-brute`, `ldap-brute`, `redis-brute`, `mongodb-brute`, `oracle-brute`, `socks-brute`, among others) try the shared username/password wordlists and print any valid pair in the port's script-output block. `--script-args` points them at custom `userdb=`/`passdb=` files and `unpwdb.timelimit` bounds how long the credential iteration runs.

### Scan via an FTP bounce

```
nmap -Pn -n -v -p 21,80,443 -b ftp:ftp@198.51.100.10 203.0.113.0/24
```

Outcome: `-b user:pass@host` relays every probe through the FTP server's `PORT` command, so targets see the FTP host rather than you — a route to addresses the server can reach when its FTP service still allows bounce (rare today; the `ftp-bounce` script tests whether it does). The listed ports are scanned on every target through the relay.

### SCTP scan

```
nmap -Pn -T4 -sY -n --open 198.51.100.10
```

Outcome: `-sY` (SCTP INIT) is the SCTP analogue of a SYN scan — half-open, fast and invisible to TCP/UDP-oriented defenses — and surfaces SS7/SIGTRAN-style SCTP services no TCP or UDP scan will show. `-sZ` (COOKIE-ECHO) is the quieter variant; add `-sV` to probe versions on answering ports.

### Enumerate supported IP protocols

```
nmap -Pn -sO 198.51.100.10
```

Outcome: `-sO` scans protocols instead of ports — the report lists IP protocol numbers (ICMP 1, TCP 6, UDP 17, GRE 47, OSPF 89, ...) with `open` for each one the host answers, exposing protocol handling (VPN tunneling, routing) a port scan never sees. Narrow the sweep with the ordinary port-spec option, e.g. `-p 47` for GRE only; there is no `--protocol` flag.

### Ping sweep with explicit ICMP probe types

```
nmap -PE -PP -PM -sn -n 198.51.100.0/24
```

Outcome: forces host discovery to exactly the three ICMP probes — echo (`-PE`), timestamp (`-PP`), netmask (`-PM`) — with `-sn` stopping after discovery and `-n` skipping reverse DNS. Some embedded hosts answer timestamp or netmask requests while dropping echo and TCP probes, so widening the probe set finds machines a plain `-sn` sweep reports as dead.

## Bug bounty workflow with nmap

nmap is the recon head, not the payout. On a web-centric bug bounty program the money is almost never "nmap printed a CVE" — it is the attack surface nmap uncovers that nothing else was looking at: a forgotten service on a non-standard port, an unauthenticated datastore bound to a public interface, a management console that was never meant to face the internet, a stale service banner that hands you a version to validate by hand. Treat nmap as the step that turns an in-scope IP range or resolved host set into a ranked list of things to investigate with HTTP tooling, manual testing, and targeted exploit research. Everything below assumes written authorization, a program that permits active network scanning (many do not — see scope discipline), and single-host, low-volume discipline.

**Before anything: confirm scanning is in scope.** A large fraction of public programs restrict testing to specific web origins (`*.example.com` over 80/443) and explicitly forbid network/port scanning, automated tooling, or anything touching cloud-provider infrastructure. Read the rules of engagement first. If the scope is "the web application," nmap's role shrinks to confirming which ports serve the app and what terminates TLS — do that and stop. The full workflow below applies only when the program grants network-level testing of owned IP ranges.

### Phase 0 — Scope resolution and target list

Goal: turn the program's scope (domains, CIDRs, ASNs) into a concrete, de-duplicated, in-scope target file — and nothing outside it.

- Resolve in-scope hostnames to addresses yourself and confirm each address sits inside an in-scope CIDR before you send a single packet. Cloud-hosted apps share IPs; the address behind `app.example.com` may be a CDN edge or a shared load balancer you are not authorized to scan.
- Build an explicit target file and feed it with `-iL targets.txt`; hold out anything ambiguous with `--exclude`/`--excludefile`. `-iL -` reads from stdin for piping; `#` begins a comment line in the file.
- Never use `-iR` (random-target selection) on an engagement — it generates traffic to addresses you have no authorization for.

```
nmap -sL -n -iL targets.txt            # list-scan: expand and print targets, send no packets
```

`-sL` (list scan) resolves and prints exactly what *would* be scanned without touching any host — the cheapest possible sanity check that your target set matches scope. Diff its output against the program's CIDRs before proceeding.

### Phase 1 — Host discovery (map what is live)

Goal: learn which in-scope addresses answer, so you spend port-scan budget only on live hosts. On a single authorized web host you can skip straight to `-Pn` port scanning; discovery matters when a CIDR is in scope.

```
nmap -sn -PE -PS443,80 -PA3389 --max-rate 100 -oA recon/discovery -iL targets.txt
```

`-sn` stops after discovery (no port scan). The default probe mix is already `-PE -PS443 -PA80 -PP`; widening the SYN/ACK ports catches hosts that drop ICMP. Keep `--max-rate` low on a shared or cloud range so a sweep does not read as an attack. Firewalled hosts that drop every discovery probe appear dead — if the program says a host exists, fall back to `-Pn` and let the port scan decide liveness. On a local/VPN segment nmap uses ARP/Neighbor Discovery by default; `--disable-arp-ping` forces probe-based liveness on proxy-ARP networks where ARP always answers.

### Phase 2 — Port discovery (the cost-math phase)

Goal: find every open port without a scan so heavy it trips rate limits or misses ports. This is where top-ports-vs-full-range economics decide your run.

The default scan covers the top 1000 TCP ports (ranked by observed frequency in `nmap-services`). `--top-ports N` sets the count explicitly; `-p-` is all 65535. The cost is roughly linear in port count per host, so:

- **Top 1000** (`--top-ports 1000`, the default): the fast first pass. Misses services parked on deliberately obscure ports — exactly where forgotten dev/admin services hide.
- **Top 3000–5000** (`--top-ports 3000`): a reasonable middle ground that catches most real-world non-standard placements at a few times the cost.
- **Full range** (`-p-`): the only way to guarantee nothing is missed, at ~65× the top-1000 packet budget. Reserve it for high-value hosts, and split the work: fast port-find, then deep-probe only the open ports.

```
# Stage 1: find open TCP ports fast, no version/script work yet
nmap -p- --min-rate 500 -T3 -oA recon/allports 198.51.100.10

# Stage 2: deep-probe only the ports stage 1 found open
ports=$(nmap -p- --min-rate 500 198.51.100.10 -oG - | grep -oP '\d+/open' | cut -d/ -f1 | paste -sd,)
nmap -p "$ports" -sV -sC --version-light -oA recon/deep 198.51.100.10
```

`--min-rate 500` sets a packets-per-second *floor* so a full-range sweep finishes in reasonable time; drop it (or set `--max-rate`) on fragile or shared targets. Staging means the expensive `-sV`/`-sC` probes hit a handful of open ports instead of 65535, which is the difference between minutes and hours. UDP is a separate, far slower axis — start with `--top-ports 20 -sU` and widen only if a UDP service is suspected (SNMP 161, DNS 53, SNMP/TFTP/NTP leaks are common UDP-only finds).

### Phase 3 — Service and version detection depth

Goal: name the software and version behind each open port precisely enough to drive CVE research — without over-probing.

`-sV` turns the open-port list into a `PORT STATE SERVICE VERSION` table. Depth is governed by `--version-intensity <0-9>` (**default 7**): each probe in `nmap-service-probes` carries a rarity from 1–9, and intensity selects how many are tried. Lower = fewer probes, faster, quieter; higher = more probes, slower, louder, better identification.

- `--version-light` (= `--version-intensity 2`): the bounty default. Far fewer probes, much quieter, still names the common services. Start here.
- `--version-intensity 7` (the plain `-sV` default): more thorough; use on a host where light detection returned `tcpwrapped` or an unnamed version.
- `--version-all` (= `--version-intensity 9`): every probe against every port. Loud and slow — reserve for one stubborn high-value port, never a range.

Port-pinned probes (DNS on 53, the SSL probe on 443) always run regardless of intensity. `--allports` additionally probes TCP 9100, which `-sV` skips by default because some printers print whatever is sent there — only use it when a printer-free host has something on 9100. Capture the exact version strings verbatim; they are the evidence and the CVE-lookup key.

### Phase 4 — NSE enumeration (safe first, intrusive only with permission)

Goal: pull structured detail out of each service — TLS config, HTTP titles and headers, SMB/SNMP/LDAP metadata, exposed shares — using scripts whose category you have deliberately chosen.

NSE scripts are tagged by category, and the category tells you whether a script is program-safe. The dividing line matters for staying inside scope:

| Category | What it does | Bounty-safe? |
|---|---|---|
| `safe` | Won't crash services, hog bandwidth, or exploit holes | Yes — the default-safe tier |
| `default` | The `-sC`/`-A` set; chosen for speed, usefulness, reliability | Mostly — "almost always" safe, but a few members are mildly intrusive; the `-sC` docs warn some need permission |
| `version` | Extends `-sV`; runs only when `-sV` is given, cannot be selected alone | Yes |
| `discovery` | Queries registries, SNMP, directory services for more detail | Usually — read the individual script |
| `auth` | Deals with (not brute-forcing) auth credentials | Usually, but check each |
| `intrusive` | Risks crashing the target, heavy resource use, or looking malicious | **No** — explicit permission only |
| `brute` | Brute-forces service credentials | **No** — credential attack; separate authorization |
| `dos` | May cause denial of service | **Never** on a bounty target without written DoS permission |
| `exploit` | Actively exploits a vulnerability | **No** — this is exploitation, not recon |
| `fuzzer` | Sends randomized/malformed fields; slow, bandwidth-heavy | **No** — treat as intrusive |
| `malware` | Checks for backdoors/malware infection | Safe to run, rarely relevant |
| `external` | Sends data to a third-party service (e.g. `vulners` queries vulners.com) | Caution — leaks target data off-site; may breach program privacy rules |
| `broadcast` | Discovers hosts via local broadcast | Local-segment only; can add out-of-scope hosts via `newtargets` |

```
# Safe, selective enumeration — explicit script list beats a blind category sweep
nmap -sV --version-light -p 443,8443 --script "ssl-cert,ssl-enum-ciphers,http-title,http-headers,http-security-headers" -oA recon/web 198.51.100.10

# Category selection with boolean logic — "safe discovery, but drop anything external"
nmap -sV -p 445 --script "safe and smb-* and not brute" -oA recon/smb 198.51.100.10
```

`--script` accepts script names, categories, `directory/`, wildcards (`http-*`), and boolean expressions with `and`/`or`/`not` and parentheses (`not` binds tightest, then `and`, then `or`); a comma list is equivalent to `or`. Quote any expression containing spaces. Check what a selection *would* run before you run it:

```
nmap --script-help "default and safe"        # name + categories + description, no packets sent
```

The `vulners` script (categories `vuln`, `safe`, `external`) maps detected `-sV` versions to known CVEs — a useful lead generator, but it ships your service-version data to vulners.com, so confirm the program permits third-party lookups before using any `external` script. Everything it reports is a *lead to validate*, never a finding on its own.

### Phase 5 — Evidence capture and handoff

Goal: preserve machine-readable output for the record and pipe the interesting ports into the tools that actually confirm findings.

```
nmap -sV --version-light -sC -p "$ports" -oA scans/example-host-$(date +%F) 198.51.100.10
```

Always scan with `-oA <basename>` so every run leaves three artifacts — `.nmap` (human report), `.gnmap` (grepable, one line per host), `.xml` (for tool import). The open web ports feed httpx/nuclei/a browser; exposed datastores feed a manual unauthenticated-access check; stale versions feed CVE research. nmap's job ends at "here is what is reachable and what it runs" — the finding is proven downstream.

## Where the money is with nmap

nmap rarely *is* the finding; it is the discovery that makes a finding possible. Prioritise by what the open port + version actually exposes, not by scan completeness.

| Finding class | What reaches it | Typical severity | Dupe risk |
|---|---|---|---|
| Unauthenticated datastore on a public interface (Redis 6379, MongoDB 27017, Elasticsearch 9200, Memcached 11211, CouchDB 5984) | `-p-` finds the port; `-sV` + a manual unauth connect confirms no auth | Critical | Low–Med — if you got there first |
| Exposed container/orchestration API (Docker 2375, Kubelet 10250, etcd 2379) | Full-range scan + `-sV`; confirm unauth with the API's own client | Critical | Low |
| Management/admin console on a non-standard port (Jenkins, RabbitMQ mgmt 15672, Grafana, phpMyAdmin, Actuator) | `--top-ports 3000`+ or `-p-`; `http-title`/`http-headers` to identify | High–Critical | Medium |
| Outdated service version → CVE lead | `-sV` version string → manual CVE research → validated PoC | High–Critical (once validated) | Varies — never report the version alone |
| Exposed dev/staging/debug service | `-p-` + `-sV`; banners and titles reveal non-prod intent | Medium–High | Medium |
| Default/weak service credentials | `brute`-category NSE — **separate authorization required** | High–Critical | Low |
| Info disclosure via service metadata (`smb-os-discovery`, `snmp-info`, `nbstat`, `ssl-cert` SANs) | Targeted `safe` NSE; SANs often leak internal hostnames/new subdomains | Low–Medium | High |
| TLS/SSL misconfiguration (weak ciphers, expired/mismatched cert) | `ssl-enum-ciphers`, `ssl-cert` | Low–Info | High — most programs treat as hardening |
| Open resolver / mail relay / amplification vector | `-sU` + `dns-recursion`, `smtp-open-relay` (test gently) | Medium | Medium |

Two rules keep these from being rejected: a version number is a *lead*, not a vulnerability — triagers close "outdated nginx" with no PoC as informational; and an open port is not an exposure until you demonstrate what it lets an unauthenticated attacker do. The payout is the demonstrated impact, written up with the nmap output as the discovery evidence.

## Command recipes

Annotated, low-volume commands. Defaults shown are the program-safe ones; the aggressive knobs are called out so you can see what you are turning up.

```
# Scope sanity check — resolve + print targets, zero packets to any host
nmap -sL -n -iL targets.txt
# -> one "Nmap scan report for <ip>" line per target, "0 hosts up". Diff against the program CIDRs.
```

```
# Polite host discovery across an authorized CIDR
nmap -sn --max-rate 100 -oA recon/live 198.51.100.0/24
# -> "Host is up" lines only. --max-rate 100 caps packets/sec so the sweep stays quiet.
```

```
# Fast open-port find, then deep-probe only the open ports (the two-stage standard)
nmap -p- --min-rate 500 -T3 -oA recon/ports 198.51.100.10
ports=$(grep -oP '\d+/open' recon/ports.gnmap | cut -d/ -f1 | paste -sd,)
nmap -p "$ports" -sV --version-light -sC -oA recon/deep 198.51.100.10
# --min-rate 500 is a floor for the find pass; --version-light keeps the deep pass quiet.
# For a fragile/shared target, replace --min-rate 500 with --max-rate 100 and drop -T3 to -T2.
```

```
# Quiet version detection on just the web ports
nmap -sV --version-light -p 80,443,8080,8443 -oA recon/web 198.51.100.10
# -> PORT STATE SERVICE VERSION table. --version-light = --version-intensity 2 (fewest probes).
# Escalate ONE stubborn port with: nmap -sV --version-all -p 8443 198.51.100.10
```

```
# Explicit, safe NSE on a service — named scripts, no blind category sweep
nmap -sV -p 443 --script "ssl-cert,ssl-enum-ciphers,http-title,http-security-headers" 198.51.100.10
# -> cert SANs (new subdomains!), cipher grades, page title, missing-header summary.
```

```
# Preview what a category selection runs BEFORE sending packets
nmap --script-help "discovery and safe and not external"
# -> name, categories, description for each matching script. No host is contacted.
```

```
# Narrow UDP sweep — slow protocol, so start with the top 20 ports only
sudo nmap -sU --top-ports 20 -sV --version-light --max-retries 2 -oA recon/udp 198.51.100.10
# -> SNMP/DNS/TFTP/NTP services that never appear in a TCP scan. --max-retries 2 bounds UDP's retransmit cost.
```

```
# Timing templates, from safest to loudest (know what each sets)
nmap -T2 ...   # polite: 0.4s between probes, low bandwidth/target load — good default for shared infra
nmap -T3 ...   # normal: the default, does nothing on its own
nmap -T4 ...   # aggressive: --max-rtt-timeout 1250ms --min-rtt-timeout 100ms
               #             --initial-rtt-timeout 500ms --max-retries 6, max TCP scan-delay 10ms
nmap -T5 ...   # insane: --max-rtt-timeout 300ms --max-retries 2 --host-timeout 15m — accuracy loss likely
# -T0 (5 min between probes) and -T1 (15s) are IDS-evasion speeds, not bounty tools.
```

Low-volume defaults worth standardising on: start with `--version-light`; prefer `--max-rate` (a ceiling) over `--min-rate` (a floor) on anything shared or cloud-hosted; keep NSE to named `safe`/`default` scripts; never enable `dos`/`exploit`/`fuzzer`/`brute` without separate written permission; and always write `-oA` so the run is reproducible and reportable.

## Writing a custom NSE script

When no bundled script checks the exact thing you need — a vendor-specific banner, a custom health endpoint that leaks a version, a header a program keeps setting — a short NSE script turns a manual curl loop into a repeatable, evidence-producing check that runs inside the same scan.

A script is Lua with a few required head fields and a rule + action:

```lua
description = [[
Checks an HTTP service for a custom X-App-Version header and reports it.
]]
author = "researcher"
license = "Same as Nmap--See https://nmap.org/book/man-legal.html"
categories = {"safe", "discovery"}

local http = require "http"
local shortport = require "shortport"
local stdnse = require "stdnse"

-- portrule decides WHETHER action runs; here: any open HTTP/HTTPS port
portrule = shortport.http

action = function(host, port)
  local resp = http.get(host, port, "/")
  local ver = resp.header["x-app-version"]
  if ver then
    return stdnse.format_output(true, "X-App-Version: " .. ver)
  end
  -- returning nil produces no output for this port
end
```

Key facts that keep a custom script correct and safe:

- **Head fields** are `description` (long-bracket string; first paragraph stands alone as the summary), `author`, `license`, `categories`, and optionally `dependencies`. Tag honestly — if your script could crash or flood a service, it is `intrusive`, not `safe`. That tag is what lets a teammate (or future you) trust a `"safe"` category sweep.
- **Rule types**: `portrule`/`hostrule` gate `action` on a port or host; `prerule`/`postrule` run before/after the scan (always true) for setup/summary work. `shortport.port_or_service(443,"https")` and `shortport.http` are the common port gates.
- **The `action` return** is what prints under the port. Return a table (formatted into indented keys, insertion order preserved via `stdnse.output_table()`) or a string; return `nil` to print nothing — scripts should generally only speak on success, so a clean scan stays readable.
- **Register it**: drop the `.nse` file in the scripts directory and run `nmap --script-updatedb` once so it is selectable by category, then `nmap --script my-check -p 443 198.51.100.10`. Or point `--script` straight at the file path during development.
- **Debug with `--script-trace`** (application-level I/O for every script) when a rule or request misbehaves; `--packet-trace` enables it too.

A custom `safe` script that documents exactly what it probed is also better *evidence* than a manual one-off — it is re-runnable by the triager and self-describing via `--script-help`.

## Pitfalls, noise and scope discipline

How hunters get reports rejected, accounts banned, or (worst) cause real damage with nmap:

- **Scanning out of scope.** The most common ban. Resolve every hostname and confirm the address is in an in-scope CIDR *before* scanning — cloud apps share IPs, and scanning the shared LB or CDN edge behind `app.example.com` hits infrastructure you were never authorized to touch. Use `-sL` to preview the target set and `--exclude`/`--excludefile` to fence off ambiguous addresses. Never `-iR`.
- **Ignoring a "no scanning" clause.** Many web programs forbid port scanning and automated tooling outright, or restrict testing to specific web origins. Running nmap anyway is a rules violation regardless of what you find. Read the RoE; when scope is "the web app," limit nmap to confirming the serving ports and TLS terminator.
- **Cloud-provider ToS.** Scanning AWS/GCP/Azure-hosted targets can breach the *provider's* acceptable-use policy independently of the program's permission. Some providers require advance notice for anything resembling a scan. Check both layers.
- **Intrusive NSE categories.** `--script vuln` or `--script "*"` or `-A` drags in `intrusive`, `exploit`, `dos`, `brute`, and `fuzzer` members that can crash services, lock accounts, or constitute exploitation — well past recon. Select scripts by explicit name or a `safe`/`default` category expression, and preview with `--script-help` first. `exploit`/`dos`/`brute`/`fuzzer` need separate written permission.
- **DoS by accident.** `-T5`, a high `--min-rate`, `--version-all` across a range, or `--min-parallelism` cranked up can overwhelm a fragile service or a rate-limited host — that is an unintended denial of service, and it also *corrupts your own results* (dropped probes read as closed/filtered, producing false negatives). Prefer `--max-rate` ceilings and `-T2`/`-T3` on anything you don't own outright.
- **`external` scripts leak target data.** `vulners` and other `external` scripts ship service/version data to third-party APIs. If the program's rules prohibit sharing target data with outside services, these breach them — and they add network dependencies that can stall a scan.
- **Trusting a loud scan's results.** Rate-limited or firewalled hosts silently drop probes under speed; `filtered`/`closed` from a `-T5` run can be a false negative. When a result is load-bearing for a report, re-scan the specific port slowly (`-T3`, low rate, `--max-retries` raised) and confirm.
- **Reporting a version as a vulnerability.** "Outdated OpenSSH" with no demonstrated impact is closed as informational. The nmap output is discovery evidence; the finding is the validated impact you prove downstream.
- **Noisy discovery mistaking live for dead.** A host that drops all discovery probes is reported down; add `-Pn` (skip discovery, scan anyway) when the program says the host exists, and remember `--disable-arp-ping` on proxy-ARP segments where ARP always answers and masks a filtered host.

## Reporting evidence

What to capture so a finding is reproducible and a triager can confirm it without re-running your whole methodology:

- **The exact command, verbatim**, including every flag — timing, rate caps, script selection, port list. The flags are part of the method; "`nmap -sV`" and "`nmap -sV --version-all`" are different experiments.
- **`-oA` output set**, so the `.nmap` (readable), `.gnmap` (grepable), and `.xml` (tool-importable) artifacts all exist. Attach or quote the relevant `.nmap` block; keep the `.xml` for the record.
- **The precise `PORT STATE SERVICE VERSION` line(s)** for the affected service — the version string is both the evidence and the CVE-lookup key. Include the `--reason` output (`nmap --reason`) when port *state* is the point, so the report shows *why* nmap called it open/filtered (e.g. `syn-ack` vs `no-response`).
- **The NSE script-output block** for any script-derived finding (cert SANs, cipher grades, SMB/SNMP metadata), copied exactly as it printed under the port.
- **The downstream confirmation**, not just the open port: the manual unauthenticated connect that proved the datastore has no auth, the HTTP request/response pair that proved the console is reachable, the PoC that proved the version is actually exploitable. nmap discovers; this is what makes it a finding.
- **A scope note**: the resolved address, the in-scope CIDR it falls in, and the timestamp — so the triager can confirm you stayed inside authorization. The `.nmap` header already stamps the Nmap version and scan start time; keep it.
- **Redaction and cleanup**: never exfiltrate more than the proof needs; if an `external`/`brute`/intrusive step ran under permission, say so explicitly in the report.

## References

- Nmap Reference Guide — host discovery: https://nmap.org/book/man-host-discovery.html
- Nmap Reference Guide — port scanning techniques: https://nmap.org/book/man-port-scanning-techniques.html
- Nmap Reference Guide — service and version detection: https://nmap.org/book/man-version-detection.html
- Nmap book — version detection (Chapter 7): https://nmap.org/book/vscan.html
- Nmap Reference Guide — timing and performance: https://nmap.org/book/man-performance.html
- Nmap Reference Guide — output: https://nmap.org/book/man-output.html
- Nmap Reference Guide — target specification: https://nmap.org/book/man-target-specification.html
- Nmap Reference Guide — miscellaneous options: https://nmap.org/book/man-misc-options.html
- NSE usage (script categories, `--script` expressions, flags): https://nmap.org/book/nse-usage.html
- NSE tutorial (writing a script): https://nmap.org/book/nse-tutorial.html
- NSE API and output formatting: https://nmap.org/book/nse-api.html

*Research supplement added 2026-11-15, grounded in the official Nmap Reference Guide and NSE documentation at nmap.org/book (host-discovery, port-scanning-techniques, version-detection, performance, output, target-specification, misc-options, nse-usage, nse-tutorial, nse-api) (model: claude-opus-4-8).*
