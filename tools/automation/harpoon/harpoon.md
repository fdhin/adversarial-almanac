---
name: "harpoon"
categories:
  - blackarch-automation
version: "388.4bb8250"
homepage: "https://github.com/Te-k/harpoon"
packaged-by: [blackarch]
blackarch-builddate: "2026-05-31"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "Te-k/harpoon"
github-stars: 1299
github-pushed: "2026-09-11"
github-archived: false
github-issues: 76
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# harpoon

> Plugin-based OSINT and threat-intelligence CLI that fronts dozens of APIs (VirusTotal, Shodan, Censys, crt.sh, OTX, MISP, ...) from one command.

## What it's for

Harpoon (Te-k) is an OSINT / threat-intel CLI: a single `harpoon` command with a plugin per source — DNS and passive DNS, certificate transparency, VirusTotal, Shodan, Censys, OTX, MISP, Have I Been Pwned, GreyNoise, SecurityTrails, Robtex, Tor exit-node checks, and many more. Each plugin needs its API keys entered once via `harpoon config` (stored in `~/.config/harpoon/config`); a handful of plugins (crt.sh, DNS, Tor) work without keys. It gathers and displays indicators for domains, IPs, hashes, emails, and ASNs — it is a lookup front end, not a scanner. Unrelated to the intrusion-malware family of the same name.

## Typical use cases

### Configure API keys and check plugin readiness

```
harpoon config
harpoon update
harpoon config -c
```

Outcome: the first command interactively prompts for each supported API key and writes `~/.config/harpoon/config`; `update` downloads the offline databases (GeoIP, ASN data); `config -c` reports which plugins are fully configured and which still lack keys.

### Pull a domain's certificates

```
harpoon crtsh domain example.com
```

Outcome: every certificate logged in crt.sh's certificate-transparency data for the domain is listed — subdomains, issuance dates, and serials — a quick map of an organization's TLS footprint.

### Map DNS for a host

```
harpoon dns example.com
```

Outcome: resolves and displays the domain's DNS records; the same command accepts an IP, and `-e`/`--extended` adds deeper record enumeration.

### Triage an IP address

```
harpoon ip 198.51.100.10
```

Outcome: consolidates geo-location, ASN ownership, and reverse data for the address in one view — a standard first pass when triaging a log entry.

### Enrich a file hash from VirusTotal

```
harpoon vt hash 44d88612fea8a8f36de82e1278abb02f
```

Outcome: VirusTotal's detection record for the hash is fetched and summarized (subcommands like `vt domain` exist for other indicator types); requires a configured VT API key.

*No corpus evidence — derived from upstream documentation.*
