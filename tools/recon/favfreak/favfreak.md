---
name: "favfreak"
categories:
  - blackarch-recon
version: "27.8acea5e"
homepage: "https://github.com/devanshbatham/FavFreak"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "devanshbatham/FavFreak"
github-stars: 1318
github-pushed: "2023-08-29"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# favfreak

> Groups a list of URLs by favicon hash (mmh3), fingerprinting the technology behind each host and generating Shodan dorks from the hashes.

## What it's for

FavFreak (devanshbatham) weaponizes favicon.ico for bug bounty and OSINT recon: it takes a list of URLs on stdin, fetches each host's `/favicon.ico`, computes the mmh3 hash, and sorts all input domains/subdomains/IPs by that hash. Because identical deployments share identical favicons, the grouped output immediately clusters related infrastructure, and each calculated hash is matched against a built-in fingerprint dictionary naming known technologies; Shodan dorks (`http.favicon.hash:<hash>`) can be generated for hunting further hosts. Reach for it after subdomain enumeration to cluster targets by technology. Input URLs must begin with `http://` or `https://`, and the fingerprint dictionary lives inside `favfreak.py` where you can add your own hash→name entries. It hashes and clusters only — it does not port-scan the discovered hosts.

## Typical use cases

### Cluster URLs by favicon hash

Pipe your subdomain list through it.

```
cat urls.txt | python3 favfreak.py
```

Outcome: fetches `/favicon.ico` from every URL in `urls.txt`, prints each host's calculated favicon hash, then a sorted grouping of all domains/IPs per hash — hosts sharing a hash run the same stack — plus fingerprint-dictionary matches naming recognized technologies.

### Save results to a file

Keep the clustered output for later.

```
cat urls.txt | python3 favfreak.py -o output
```

Outcome: same enumeration and hash-grouping as above, written to the output file so the clustered inventory survives the terminal session; the printed hash values double as `http.favicon.hash:` search terms for Shodan.

No corpus evidence — derived from upstream documentation.
