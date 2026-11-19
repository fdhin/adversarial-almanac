---
name: "protos-sip"
categories:
  - blackarch-voip
version: "2"
homepage: "https://www.ee.oulu.fi/research/ouspg/PROTOS_Test-Suite_c07-sip"
packaged-by: [blackarch, kali]
blackarch-builddate: "2014-05-29"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "foreni-packages/protos-sip"
github-stars: 0
github-pushed: "2015-09-20"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# protos-sip

> The OUSPG c07-sip test suite: a jar of systematically malformed SIP INVITEs for robustness testing.

## What it's for

protos-sip packages the University of Oulu OUSPG PROTOS c07-sip test suite (release 2), a classic black-box robustness test set for SIP implementations: a Java jar of systematically malformed INVITE requests fired at a SIP device to expose parsing and state-machine flaws. The package installs the jar at `/usr/share/protos-sip` and a `protos-sip` wrapper that runs it with `java -jar c07-sip-r2.jar`. It is a test harness, not a scanner — it sends the cases you aim at the target you choose, and you watch (with a sniffer or crash logs) which case breaks the implementation. The same c07-sip jar is one suite among the multi-protocol PROTOS collection (HTTP, LDAP, SNMP, DNS, ISAKMP, H.225) packaged separately as `protos` under the fuzzer category.

## Typical use cases

### Fire the whole suite at a SIP server

```
protos-sip -touri user@example.com -sendto 198.51.100.10
```

Outcome: sends the malformed INVITE test cases one after another (100 ms apart by default, `-delay` to change) from local port 5060 to 198.51.100.10:5060 (`-dport` to change); run a sniffer alongside and note the case index at which the target misbehaves or stops responding.

### Check whether the target survives each case

```
protos-sip -touri user@example.com -sendto 198.51.100.10 -validcase -showreply
```

Outcome: after every test case the tool sends a valid case and waits for the response, so when replies stop arriving you know which index killed the target; `-showreply` additionally prints every response received and `-teardown` sends CANCEL/ACK to clean up dialogs.

### Replay a single test case or range

```
protos-sip -touri user@example.com -sendto 198.51.100.10 -single 4522
```

Outcome: injects only test case number 4522 — or a contiguous range with `-start`/`-stop` — the quickest way to re-check one known-bad message after a fix; `-file <file>` sends your own raw message instead of a built-in case.

*No corpus evidence — derived from upstream documentation.*
