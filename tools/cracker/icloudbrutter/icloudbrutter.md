---
name: "icloudbrutter"
categories:
  - blackarch-cracker
version: "15.1f64f19"
homepage: "https://github.com/m4ll0k/iCloudBrutter"
packaged-by: [blackarch]
blackarch-builddate: "2018-08-06"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "m4ll0k/iCloudBrutter"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# icloudbrutter

> AppleID dictionary attack and password spray with proxy and Tor support

## What it's for

iCloudBrutter is a small Python 3 script that runs dictionary attacks against AppleID authentication: either one account against a password list, or a list of Apple IDs against a password list (a spray). It sends HTTP Basic-style requests to the Apple endpoint, prints each attempt, stops at the first valid password, and explicitly reports when the service starts blocking. `--proxy` routes traffic through a chosen proxy and `--tor` through a local Tor SOCKS listener. The upstream repository is gone (surviving forks carry the code), and Apple's endpoint now rate-limits and locks accounts aggressively, so treat this as a legacy brute forcer for studying the technique, not a practical one.

## Typical use cases

### Dictionary attack on one AppleID

```
icloudbrutter --id user@example.com --wordlist passwords.txt
```

Outcome: prints the banner, then `[ * ] Trying with password: <candidate>` for each line of passwords.txt, stopping with `[ + ] Password found: <password>` on success or reporting `[ ! ] Blocked!!!` when the endpoint refuses further attempts.

### Spray an ID list through Tor

```
icloudbrutter --idw ids.txt --wordlist passwords.txt --tor
```

Outcome: walks every Apple ID in ids.txt through the password list, sending the requests through a SOCKS proxy at 127.0.0.1:9050 (assumes Tor running locally); `--proxy <host>` routes through a specified proxy instead.

*No corpus evidence — derived from upstream documentation.*
