---
name: "dnsobserver"
categories:
  - blackarch-networking
version: "5.f331482"
homepage: "https://github.com/allyomalley/dnsobserver"
packaged-by: [blackarch]
blackarch-builddate: "2022-02-25"
build-system: go
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "allyomalley/dnsobserver"
github-stars: 193
github-pushed: "2020-09-06"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dnsobserver

> Self-hosted authoritative DNS service that turns out-of-band DNS callbacks into Slack alerts.

## What it's for

DNSObserver (Go, by Ally O'Malley) runs on your own server as the authoritative name server for a domain you delegate to it (glue records ns1/ns2.<domain> pointing at the host) and watches every query for that domain or any subdomain. Any blind vulnerability that can be pointed at <anything>.example.com — blind OS command injection, blind SQLi, blind XXE, SSRF — produces a lookup, and DNSObserver reports it via a Slack webhook (or to stdout when no webhook is given). It answers A queries for its own nameservers and the domain apex with your server IP, returns empty answers for everything else unless you configure custom A records, and does not proxy DNS or serve arbitrary zones.

## Typical use cases

### Run it as an out-of-band detection nameserver

```
sudo dnsobserver --domain example.com --ip 198.51.100.10 --webhook https://hooks.slack.com/services/XXX/YYY/ZZZ
```

Outcome: binds 53/udp and 53/tcp for example.com; a test lookup of example.com (or any subdomain) from any machine produces a Slack notification carrying the request details. Omit --webhook and every interaction is logged to standard output instead.

### Answer chosen hostnames with custom A records

Resolve specific names to addresses you control — for example a callback host a blind vulnerability will fetch — while still detecting every other subdomain query.

```
dnsobserver --domain example.com --ip 198.51.100.10 --recordsFile records.yml
```

with records.yml:

```
a_records:
  - hostname: "callback.example.com"
    ip: "198.51.100.10"
```

Outcome: A lookups for callback.example.com now resolve to 198.51.100.10 instead of an empty answer, while queries for any other name under the domain still trigger notifications. Only A records are supported.

*No corpus evidence — derived from upstream documentation.*
