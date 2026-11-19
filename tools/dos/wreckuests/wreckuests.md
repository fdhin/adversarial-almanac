---
name: "wreckuests"
categories:
  - blackarch-dos
version: "75.69b6c27"
homepage: "https://github.com/JamesJGoodwin/wreckuests"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "JamesJGoodwin/wreckuests"
github-stars: 173
github-pushed: "2021-05-18"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# wreckuests

> HTTP GET/POST flood tool that gives every proxy in your list its own attacking thread.

## What it's for

Wreckuests runs HTTP-flood (GET/POST) attacks through proxy servers acting
as improvised "bots": it reads one proxy per line from `files/proxy.txt` and
spawns a thread per proxy, so the flood originates from many addresses.
Each request is disguised with randomized URL parameters (cache bypass),
rotated User-Agents and Referers, automatic gzip/deflate toggling, optional
HTTP basic-auth credentials, and CloudFlare presence is detected and
reported. The author is explicit that it is not a one-click takedown tool —
each target needs its own analysis. Run it as root (raw per-thread volume);
too many proxies can crash it with a segmentation fault. Educational use
against systems you are authorized to test.

## Typical use cases

### Flood a heavy endpoint through your proxy list

Aim the distributed GET flood at the target's most expensive resource.

```
sudo python3 wreckuests.py -v http://target.example.com/big.jpg
```

Outcome: one thread per proxy in `files/proxy.txt` hammers the URL with
cache-busting parameters and rotated headers; dead proxies are kicked with
a printed notice. Flood continues until interrupted with Ctrl+C.

### Attack a basic-auth-protected page

Bypass HTTP basic authentication while flooding.

```
sudo python3 wreckuests.py -v http://target.example.com/profile.php -a user:Password1 -t 15
```

Outcome: sends authenticated requests (user/Password1) through the proxy
fleet with a 15-second connect/read timeout instead of the default 10,
letting slower proxies still land hits on the protected page.

### Tune the timeout for fast proxies

Raise request intensity when the whole proxy list is low-latency.

```
sudo python3 wreckuests.py -v http://target.example.com -t 5
```

Outcome: connection and read timeout drops to 5 seconds, increasing request
intensity per thread; slow proxies that cannot connect within the window
simply never land, so use only with a fast list.

*No corpus evidence — derived from upstream documentation.*
