---
name: "hasere"
categories:
  - blackarch-recon
version: "1.0"
homepage: "https://github.com/galkan/hasere"
packaged-by: [blackarch]
blackarch-builddate: "2014-08-02"
build-system: scripts
license: custom
meta-checked: "2026-11-09"
github-repo: "galkan/hasere"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# hasere

> Discover the vhosts on a subnet with Bing's ip: index, then hunt juicy filetypes via Google.

## What it's for

hasere is a three-stage vhost enumerator from the pre-Shodan era: given a
CIDR subnet, it (optionally) nmaps the range for hosts with 80/443 open,
asks Bing which hostnames are indexed for each IP address, and then queries
Google for the configured file types on every discovered hostname —
surfacing both shared-hosting neighbours and sensitive documents (logins,
carts, admin pages) in one pass. The filetype list lives in a plain file
(the packaged `data/filetype` ships `php` and `asp` entries) passed with
`-f`, and `-t` bounds each request's timeout. The upstream repository is
gone from GitHub; it is Python-2-era scraping against search pages, so
expect engine drift to affect the Google stage in particular — the Bing
vhost-discovery core is what it is used for.

## Typical use cases

### Map the vhosts on a subnet

```
hasere -s 198.51.100.0/28 -f data/filetype -t 3
```

Outcome: a table of IP address, the hostnames Bing indexes for it, and the
matching Google filetype hits (e.g. URLs ending in `.php`) for each
discovered vhost — shared-hosting neighbours made visible.

### Restrict the sweep to web-serving hosts

```
hasere -s 198.51.100.0/28 -f data/filetype -n
```

Outcome: the nmap pre-pass (`-n`, needs nmap installed) filters the subnet
to IPs with port 80 or 443 open before the Bing and Google stages run,
cutting lookups on dead addresses.

*No corpus evidence — derived from the author's release documentation via a
faithful mirror of the removed repository; flags verified against the
packaged script's argparse (-s/--subnet, -t/--timeout, -f/--filetype_path,
-n/--nmap).*
