---
name: "ivre"
categories:
  - blackarch-recon
version: "0.9.21.dev163"
homepage: "https://ivre.rocks/"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-04-12"
build-system: python
license: GPL-3.0-or-later
aliases: ["python-ivre"]
meta-checked: "2026-11-09"
github-repo: "ivre/ivre"
github-stars: 4198
github-pushed: "2026-10-30"
github-archived: false
github-issues: 70
github-match: "search"
gh-checked: "2026-11-07"
worth: 8.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 193
gh-activity-checked: "2026-11-09"
---

# ivre

> Network recon framework (DRUNK — Dynamic Recon of UNKnown networks) that stores Nmap/Masscan scan results and passive Zeek/Argus/Netflow captures in a database and lets you query, correlate and browse them like a private Shodan.

## What it's for

IVRE ("Instrument de veille sur les réseaux extérieurs") turns raw scanner output and traffic captures into a queryable database: an active branch ingests Nmap/Masscan XML plus ZGrab2/ZDNS/Nuclei/httpx JSON, a passive branch ingests Zeek, Argus, Nfdump and p0f data, and a flow branch models host-to-host traffic. You run the scanners you already use — IVRE itself no longer ships a scanner — then import, merge into "views", and explore via CLI tools (`ivre scancli`, `ivre ipinfo`, `ivre iphost`, `ivre flowcli`), a Python API (`ivre.db.db`), a built-in web UI, or an MCP server for LLM agents. Data lives in MongoDB, Elasticsearch or PostgreSQL. It is infrastructure for large or recurring recon (whole ASNs, /8s, enterprise estates), not a one-shot scanner.

## Typical use cases

### Initialize the database and load IP intelligence

```
ivre ipinfo --init
ivre ipdata --download
```

Outcome: `--init` (run once per database purpose; pipe `yes` into it to accept) clears/creates the passive collections, and `ipdata --download` fetches GeoIP/AS/country datasets (MaxMind, thyme BGP tables) that IVRE uses to annotate addresses — `1.2.3.4` becomes `FR [France] AS16276 [OVH SAS]`. Use `ivre scancli --init` to (re)initialize the scan collections the same way.

### Ingest Nmap/Masscan results and build a queryable view

```
nmap -vv -A -oX scans/routable-001.xml 198.51.100.0/24
ivre scan2db -c ROUTABLE-001 -s MySource -r scans/
ivre db2view nmap
```

Outcome: `scan2db` imports every XML file under `scans/` (Nmap `-oX` or Masscan XML; JSON for ZGrab2/ZDNS/Nuclei/httpx/dnsx/tlsx) tagged with campaign `-c` and source `-s`; `db2view nmap` merges scans from different tools into a unified view, deduplicating hosts — the standard pattern for combining a fast Masscan sweep with deep Nmap `-A` follow-ups on the same database.

### Query scan results from the command line

```
ivre scancli --port 22
```

Outcome: lists every host in the scan database with port 22 open, one JSON document per host (scripts output, service versions, OS guesses). Combine filters (`ivre help scancli` documents them) to slice by port, service, script output or network; results are also exposed over the web API at `/cgi/scans`.

### Record passive recon from a live interface or PCAP

```
mkdir logs
sudo LOG_PATH=logs/passiverecon zeek -b /usr/share/ivre/zeek/ivre/passiverecon/bare.zeek -C -i eth0
for f in logs/passiverecon.*; do ivre passiverecon2db < "$f"; done
```

Outcome: Zeek with IVRE's `passiverecon` script observes traffic (live interface here; use `-r capture.pcap` for a file) and writes log files; `passiverecon2db` loads them as passive records — DNS answers, HTTP headers, JA3 TLS fingerprints, p0f-style OS hints. You can also pipe directly: `zeek -b ... | ivre passiverecon2db`.

### Ask the passive database about a host or network

```
ivre ipinfo 198.51.100.10
ivre ipinfo 198.51.100.0/24
```

Outcome: prints everything passively observed about the address (or the whole /24) — country/AS, resolved domains, server headers per port, certificate chains with SANs, JA3 hashes, each with observation counts and timestamps. The companion `ivre iphost 198.51.100.10` does the reverse lookup against passive DNS records.

### Run your own passive DNS service

```
ivre iphost example.com
```

Outcome: after DNS answers have accumulated in the passive database (via the Zeek pipeline above), this prints every A/MX/… record seen for the name, with the resolver that returned it and first/last-seen timestamps — a private passive-DNS lookup over your own capture points instead of a public aggregator.

### Analyze network flows between hosts

```
zeek -r capture.pcap
ivre zeek2db ./*.log
ivre flowcli
```

Outcome: the flow branch (beta) ingests Zeek logs — or Argus/nfdump files via `ivre flow2db flows.argus`, or iptables logs via `ivre flow2db -t iptables syslog.log` — and `flowcli` explores the resulting host-to-host flow graph (filters, node search, timelines); the same data is browsable at `flow.html` in the web UI.

### Serve the web UI

```
ivre httpd
```

Outcome: starts IVRE's built-in HTTP server for the Web UI on localhost — the interactive map/filter interface over scans, passive data and views, with `/doc/` serving the rendered documentation and the web API under `/cgi/`. Behind nginx, point it at IVRE's example config instead.

*No corpus evidence — derived from upstream documentation (README plus doc/usage/* of the current master, where the old built-in `runscans` scanner no longer exists).*
