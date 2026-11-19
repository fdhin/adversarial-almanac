---
name: "fbi"
categories:
  - blackarch-social
version: "28.0f94e99"
homepage: "https://github.com/xHak9x/fbi"
packaged-by: [blackarch]
blackarch-builddate: "2020-04-27"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "xHak9x/fbi"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# fbi

> Interactive Facebook account information gatherer that aimed to pull email, phone, date of birth, residence and occupation even from profiles set to "only me".

*No corpus evidence — derived from upstream documentation.*

## What it's for

A python2-era script (circa 2018–2020) from when Facebook's platform interfaces leaked profile fields regardless of privacy settings: run it, supply the target account, and it collected the personal details above. Its collection paths depended on API surfaces Facebook has since closed, and its upstream repository has been deleted, so it survives as a snapshot of that OSINT era rather than a working tool — python2 itself is end-of-life. The archived README instructs disabling any VPN before use and warns against heavy use; BlackArch packages the script as the `fbi` command.

## Typical use cases

### Launch the gatherer against an account

Open the interactive prompt and collect what it can.

```
fbi
```

Outcome: starts the interactive console — type `help` for the command menu, enter the target profile when prompted, and the tool reports the account information its (now largely defunct) collection paths return.
