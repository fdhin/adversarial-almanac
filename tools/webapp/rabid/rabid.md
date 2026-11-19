---
name: "rabid"
categories:
  - blackarch-webapp
version: "v0.3.0.r34.ga3fc24d"
homepage: "https://noraj.github.io/rabid/"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-10-31"
build-system: ruby
license: MIT
meta-checked: "2026-11-09"
github-repo: "noraj/rabid"
github-stars: 11
github-pushed: "2026-11-08"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 6.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 37
gh-activity-checked: "2026-11-09"
---

# rabid

> CLI tool and Ruby library to decode all four formats of F5 BigIP persistence cookies.

## What it's for

F5 BIG-IP load balancers encode the backend server's IP and port inside
persistence cookies such as `BIGipServer<pool>=...`, `rd5...`, `vi...` or an
encrypted-looking base64 blob. During reconnaissance these cookies leak the
internal addressing of the pool members behind the load balancer. rabid
(short for RApid Big IP Decoder) takes a raw cookie value and prints the pool
name, the cookie type, and the decoded IP address and port. It supports all
four cookie formats and works both as a `rabid` command and as the
`bigipcookie` Ruby library. It decodes only — it neither crafts nor modifies
cookies.

## Typical use cases

### Decode an IPv4 BigIP cookie

Pass a raw cookie string to reveal the backend host.

```
rabid 'BIGipServer<pool_name>=1677787402.36895.0000'
```

Outcome: prints the pool name, cookie type, raw cookie and the decoded pool
member as `IP:port`; the internal IP is what you note down for further
testing of the backend.

### Decode routed or IPv6 cookie formats

rabid auto-detects the IPv4, routed (`rd5o...`), IPv6 (`vi...`) and
IPv6-in-routed (`rd3o...`) cookie encodings.

```
rabid 'rd5o00000000000000000000ffffc0000201o80'
rabid 'CustomeCookieName=vi20010112000000000000000000000030.20480' --ipv6-long-format
```

Outcome: each command decodes its cookie to an IPv4-mapped or IPv6 address
plus port; `--ipv6-long-format` prints the uncompressed IPv6 form.

### Scriptable short output

Strip the prose and keep only the decoded value for piping.

```
rabid 'BIGipServer~SuperPool=rd3o20010112000000000000000000000030o80' --short --no-color
```

Outcome: prints a single line containing only the decoded cookie, easy to
capture in shell scripts; `--no-color` removes ANSI codes.

*No corpus evidence — derived from upstream documentation (noraj/rabid README, docs and `bin/rabid` docopt usage; BlackArch builds noraj/rabid, the maintained fork of Orange-Cyberdefense/rabid).*
