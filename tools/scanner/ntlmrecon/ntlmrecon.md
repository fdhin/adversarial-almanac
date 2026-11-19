---
name: "ntlmrecon"
categories:
  - blackarch-scanner
version: "81.64d7f9d"
homepage: "https://github.com/sachinkamath/ntlmrecon"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-01-13"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "sachinkamath/ntlmrecon"
github-stars: 509
github-pushed: "2025-09-23"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ntlmrecon

> Dependency-light enumerator that sprays NTLM authentication probes at URLs, IPs and CIDR ranges and tabulates the AD naming information each endpoint leaks.

## What it's for

NTLMRecon finds NTLM-enabled web endpoints and, by sending a fake authentication request and parsing the NTLMSSP response with its own pure-Python implementation, extracts the AD domain name, server name, DNS domain, FQDN and parent DNS domain per endpoint — without standing up Nmap's `http-ntlm-info` NSE against every host. Input flexibility is the point: a single URL, an IP, a CIDR range, or a mixed file of all of them, with per-line type detection and CIDR expansion. Results accumulate to a CSV file as they are found, which makes it a practical first pass when you have a large IP/domain space and need to know where the Windows infrastructure (Exchange, ADFS, autodiscover) lives and what it is called internally.

## Typical use cases

### Probe a single URL

```
ntlmrecon --input https://mail.example.com --outfile ntlmrecon.csv
```

Outcome: sends the NTLM negotiate to the endpoint and, on success, appends a row with URL, AD domain name, server name, DNS domain, FQDN and parent DNS domain to `ntlmrecon.csv`.

### Sweep a CIDR range

```
ntlmrecon --input 198.51.100.0/24 --outfile ntlmrecon-ranges.csv
```

Outcome: expands the range and probes each address for NTLM-enabled web endpoints, writing every hit to the CSV — the quick way to locate Exchange/AD-facing servers in an address block.

### Feed a mixed target file

```
ntlmrecon --input targets.txt --outfile ntlmrecon-mixed.csv
```

Outcome: each line is auto-detected as URL, hostname, IP or CIDR and handled accordingly (ranges expanded by default), so one command covers a heterogeneous target list; note there is no built-in de-duplication of expanded addresses.

*No corpus evidence — derived from upstream documentation (README example usage of sachinkamath/ntlmrecon).*
