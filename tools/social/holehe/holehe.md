---
name: "holehe"
categories:
  - blackarch-social
version: "434.14da70f"
homepage: "https://github.com/megadose/holehe"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-01-12"
build-system: python
license: GPL3
meta-checked: "2026-11-09"
github-repo: "megadose/holehe"
github-stars: 15564
github-pushed: "2024-09-10"
github-archived: false
github-issues: 122
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# holehe

> Find which of 120+ websites have an account registered to a given email.

## What it's for

Holehe determines whether an email address is attached to an account on a site by exercising that site's password-recovery, registration, or login flow and reading the response — a method that does not send anything to the target's inbox, so the account owner is not alerted. It covers Twitter, Instagram, Imgur, Adobe, Amazon and 120+ other services; where a site leaks them in the flow, it extracts the partially masked recovery email or phone number. Use it to pivot from a single email during OSINT; it is an existence checker, not an account cracker, and its per-site modules are importable into your own async Python tooling (trio/httpx).

## Typical use cases

### Check an email across all supported sites

```
holehe user@example.com
```

Outcome: prints a per-site result table — registered / not found / rate-limited — with masked recovery email or phone hints where a site returns them; a rate-limited run is usually solved by changing source IP.

### Run a single provider check as a library module

```
python3 -c 'import trio, httpx
from holehe.modules.social_media.snapchat import snapchat

async def main():
    out = []
    async with httpx.AsyncClient() as client:
        await snapchat("user@example.com", client, out)
    print(out)

trio.run(main)'
```

Outcome: prints one module's result dict — `name`, `rateLimit`, `exists`, `emailrecovery`, `phoneNumber`, `others` — so individual site checks can be embedded in a larger pipeline.

*No corpus evidence — derived from upstream documentation.*
