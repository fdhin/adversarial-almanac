---
name: "httprobe"
categories:
  - blackarch-scanner
version: "22.5555984"
homepage: "https://github.com/tomnomnom/httprobe"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2021-04-26"
build-system: go
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "tomnomnom/httprobe"
github-stars: 3122
github-pushed: "2024-06-22"
github-archived: false
github-issues: 44
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# httprobe

> Take a list of domains on stdin and return the ones that actually have a working HTTP or HTTPS server — the standard liveness filter between subdomain enumeration and content tooling.

*No corpus evidence — derived from upstream documentation (tomnomnom/httprobe README, all flags taken from its examples).*

## What it's for

httprobe (TomNomNom) is a deliberately small Go filter for recon pipelines: it reads line-delimited hostnames on stdin, tries HTTP on port 80 and HTTPS on port 443 for each, and prints the base URL of every host that responds. It does not fetch content, take screenshots or probe paths — its only job is turning a pile of DNS names into a clean list of live web roots that you feed to the next tool. Extra protocol/port pairs, concurrency and timeouts are the full option surface, which is what keeps it fast over tens of thousands of domains.

## Typical use cases

### Filter a domain list down to live web servers

```
cat domains.txt | httprobe
```

Outcome: Prints one `http://host` / `https://host` URL per responding domain (both schemes when both answer) — pipe the output straight into a content prober or save it as the live-host inventory.

### Probe services on non-standard ports

```
cat domains.txt | httprobe -p http:81 -p https:8443
```

Outcome: Adds the given protocol/port pairs on top of the default 80/443 probes, catching hosts whose web service lives on an alternate port.

### Replace the default probes entirely

```
cat domains.txt | httprobe -s -p https:8443
```

Outcome: `-s` drops the default 80/443 checks, so only the explicitly listed `https:8443` probe runs — useful when a sweep of every port would be noise you don't want.

### Speed up or slow down a large sweep

```
cat domains.txt | httprobe --prefer-https -c 50 -t 20000
```

Outcome: Runs 50 checks concurrently (`-c`, default 10), allows 20-second timeouts (`-t`, milliseconds) for slow hosts, and with `--prefer-https` prints only the HTTPS URL for domains where HTTPS already works.
