---
name: "ufonet"
categories:
  - blackarch-dos
version: "84.aea21b3"
homepage: "https://github.com/epsylon/ufonet"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "epsylon/ufonet"
github-stars: 2546
github-pushed: "2026-09-26"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 5
gh-activity-checked: "2026-11-09"
---

# ufonet

> UFONet — DDoS framework (CLI + web GUI) that weaponizes open-redirect "zombies" and amplification vectors against a target.

## What it's for

UFONet (by epsylon) is an anti-censorship/"disruptive toolkit" that performs
Layer-7 and Layer-3 stress attacks. Its signature mechanism is harvesting
open-redirect endpoints on third-party sites ("zombies") via dork searches
and using them as an improvised botnet, so attack traffic towards a target
originates from unrelated URLs rather than one host. It layers dozens of
attack engines on top — LOIC, SLOWLORIS-style slow attacks, and
amplification/reflection vectors (DNS, NTP monlist, Memcached, SSDP, SNMP,
CharGen, CLDAP, WS-Discovery and more) — plus reconnaissance tools: a port
scanner (`--xray`), target inspection with WAF/CVE detection (`-x`), and
Tor/proxy support. It runs as a single `ufonet` command in CLI mode or as a
local web GUI (`--gui`). Authorized testing only; `ufonet --examples` prints
the author's own example invocations.

## Typical use cases

### Start the web GUI

Drive UFONet from its local browser interface instead of flags.

```
ufonet --gui
```

Outcome: launches the UFONet web interface served from localhost; open the
printed URL in a browser to reach panels for botnet management, attack
configuration, maps and statistics.

### Harvest zombie open redirects with a dork

Collect third-party redirect endpoints to build the botnet.

```
ufonet -s 'proxy.php?url='
```

Outcome: queries search engines for pages matching the dork, validates the
redirect behavior, and appends confirmed zombies to `botnet/zombies.txt`
for later use. Add `--auto-search` to run the full dork list automatically,
or `--se`/`--sa` to choose or fan out engines.

### Validate the botnet

Prune dead zombies before an attack.

```
ufonet --test-offline
```

Outcome: probes every entry in the botnet and reports which zombies are
still live, discarding offline ones so attacks do not waste rounds. Use
`-t botnet/zombies.txt` to re-test a specific list.

### Profile the target first

Map the server, WAF and weak points before spending zombies.

```
ufonet -x http://target.example.com
```

Outcome: fingerprints the webserver configuration, flags known CVEs and
detects any WAF in front of the site; `-i http://target.example.com`
additionally hunts the heaviest resource on the host to aim attacks at.

### Launch a botnet-driven attack

Round-robin the zombie fleet against the target.

```
ufonet -a http://target.example.com -r 1000
```

Outcome: sends 1000 attack rounds in which each round routes abuse traffic
through the harvested open-redirect zombies, printing per-round statistics;
run under a proxy with `--proxy 'http://127.0.0.1:8118'` or force HTTPS with
`--force-ssl` as needed.

### Layer an amplification vector onto the attack

Combine the Layer-7 botnet with network amplification.

```
ufonet -a http://target.example.com --smurf 101
```

Outcome: alongside the zombie-driven HTTP attack, engages the ICMP
amplification engine for 101 rounds of reflected traffic against the
target; other vectors (e.g. `--tachyon` DNS, `--monlist` NTP, `--memcached`)
swap in the same way.

*No corpus evidence — derived from upstream documentation.*
