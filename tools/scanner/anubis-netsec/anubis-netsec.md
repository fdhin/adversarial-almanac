---
name: "anubis-netsec"
categories:
  - blackarch-scanner
version: "1.1.3.r18.g06ea822"
homepage: "https://github.com/jonluca/anubis"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-31"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "jonluca/anubis"
github-stars: 1383
github-pushed: "2026-10-26"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 11
gh-activity-checked: "2026-11-09"
---

# anubis-netsec

> Subdomain enumeration and host intelligence that collates a dozen free sources — then optionally resolves, port-scans and enriches every find.

## What it's for

Anubis (JonLuca DeCaro; PyPI package `anubis-netsec`, command `anubis`) enumerates
subdomains by collating HackerTarget, VirusTotal, certificate Subject Alternative Names
via crt.sh, NetCraft, zone-transfer checks and the community AnubisDB, with Shodan
enrichment available through an API key. Beyond listing names it can resolve every
subdomain to its IPs (`-p`), attach Shodan host details such as ISP and location (`-i`),
run an nmap service/script scan against the finds (`-n`, customizable via `-w`), recurse
into discovered subdomains (`-r`), and submit results back to AnubisDB (`-d`). It accepts
a single target or a file of them (`-t`/`-f`) and writes reports with `-o`. Traffic is
passive except for the optional nmap step.

## Typical use cases

### Enumerate a domain's subdomains

The basic discovery run.

```
anubis -t example.com
```

Outcome: checks zone transfers, SANs and each supported source, printing every discovered
subdomain with a count summary and runtime.

### Batch targets with IP resolution to a file

Sweep a scope list and keep the report.

```
anubis -f targets.txt -ip -o results.txt
```

Outcome: reads one domain per line, resolves all discovered subdomains, prints the unique
IP list per domain, and writes the combined report to `results.txt`.

### Enrich finds with Shodan and an nmap scan

Turn a name list into host intelligence.

```
SHODAN_API_KEY=yourkey anubis -t target.example.com -i -n -o scan.txt
```

Outcome: adds server location and ISP details from Shodan, then runs the default nmap
service/script scan against discovered hosts, saving everything to `scan.txt`.

### Silent list for piping

Get clean output for the next tool in the chain.

```
anubis -t example.com -S
```

Outcome: prints only the subdomains, one per line — pipe into httpx, dnsx or a shell loop
without parsing report noise.

*No corpus evidence — derived from upstream documentation (corpus hits for "Anubis" are a same-named lab asset, not this tool).*
