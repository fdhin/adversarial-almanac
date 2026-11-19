---
name: "inetsim"
categories:
  - blackarch-defensive
version: "1.3.2"
homepage: "http://www.inetsim.org/"
packaged-by: [blackarch, kali, aur]
blackarch-builddate: "2025-07-18"
build-system: perl
license: GPL3
meta-checked: "2026-11-09"
github-repo: "edgecase1/inetsim"
github-stars: 29
github-pushed: "2016-04-29"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# inetsim

> Simulates an entire internet — DNS, HTTP, SMTP, POP3 and more — on one lab host so malware samples can "phone home" safely while you watch.

## What it's for

INetSim is a Perl suite for malware analysis labs: it impersonates common internet services on a dedicated host so an analyzed sample believes it is online. Point the analysis VM's default gateway and DNS at the simulator and everything appears to work — names resolve, web servers answer, mail gets accepted — while every request is logged and every response comes from data files you control. That keeps the sample contained (no real command-and-control contact, no real exfiltration) and simultaneously exposes its behavior: requested domains, URLs, protocols and credentials. Services, bind addresses, DNS answers and fake time are all configured in inetsim.conf; it is a lab instrument, not a production service.

## Typical use cases

### Start the simulated internet for an analysis session

Bring up all enabled services for one sample run.

```
sudo inetsim --session sample-042 --bind-address 198.51.100.10
```

Outcome: starts every service enabled in the config (DNS, HTTP, SMTP, POP3, FTP, ...) bound to the given address, tags the run with the session name `sample-042`, and logs every request under the log and report directories; it keeps serving until stopped.

### Restrict which services run and what DNS answers

Tighten the lab to just the protocols a sample uses.

```
# conf/inetsim.conf
service_bind_address 198.51.100.10
dns_default_ip        198.51.100.10
start_service dns
start_service http
start_service smtp
```

Outcome: only DNS, HTTP and SMTP start, all bound to the lab IP, and DNS returns that IP for every name except entries in the hosts file — so any hostname the sample resolves "exists" and lands on the simulator.

### Fast-forward time for date-triggered samples

Make the simulated clock advance without waiting.

```
sudo inetsim --faketime-auto-delay 60 --faketime-auto-incr 86400
```

Outcome: every 60 seconds the fake time served by all services jumps 86400 seconds — one day — into the future, coaxing samples that wait for a specific date into their later-stage behavior; a negative increment runs time backwards instead.

*No corpus evidence — derived from upstream documentation.*
