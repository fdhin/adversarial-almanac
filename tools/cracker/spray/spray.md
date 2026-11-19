---
name: "spray"
categories:
  - cracker
version: "2.1+git20190226-0parrot1"
homepage: "https://github.com/Greenwolf/Spray"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot]
build-system: scripts
meta-checked: "2026-11-09"
github-repo: "Greenwolf/Spray"
github-stars: 772
github-pushed: "2024-05-25"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# spray

> Password-spraying script for Active Directory and web portals (SMB, OWA, Lync, Cisco WebVPN, OpenVPN), paced by the account lockout policy.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Spraying a few passwords across many user accounts without locking them out.
Spray (Jacob Wilkin / Greenwolf) takes the attempts-per-lockout-period and the
lockout period length in minutes as explicit arguments and spaces its guesses
accordingly over SMB (via `rpcclient`), OWA (via a captured POST request
replayed with `curl`), Lync, Cisco Web VPN and OpenVPN web portals. It ships
curated password lists of common Active Directory passwords in multiple
languages, a mode that updates those lists to the current year, and a username
generator built from first/last name lists. It is a pure bash orchestrator:
SMB spraying needs `rpcclient` (unavailable on macOS) and the HTTP modes need
`curl`. It only sprays — it does not enumerate users itself and does not
pre-check lockout policy, so the attempts/period values must come from your
own recon.

## Typical use cases

### Spray a domain over SMB

Test one password per lockout window against every user in a list.

```
spray.sh -smb 198.51.100.10 users.txt passwords.txt 1 35 CORP
```

Outcome: attempts up to 1 password per user per 35-minute lockout period
against the domain controller via `rpcclient`, printing any valid
username/password pairs; append `skipuu` as a seventh argument to skip the
automatic username-equals-password pass.

### Spray an OWA portal with a captured login request

Reuse the portal's own POST request so arbitrary web login forms can be
sprayed.

```
spray.sh -owa 198.51.100.10 users.txt passwords.txt 1 35 post-request.txt
```

Outcome: replays the captured POST request once per user/password pair with
`curl`, substituting the `sprayuser@domain.com`/`spraypassword` placeholders
from the request file, and reports which combinations authenticated;
successful hits are also written out for review.

### Spray a Lync service

Attack Microsoft Lync/Skype for Business autodiscover endpoints.

```
spray.sh -lync https://lyncdiscover.example.com/ users.txt passwords.txt 1 35
```

Outcome: for each email-style address in `users.txt` it checks authentication
against the Lync endpoint's `www-authenticate` challenge at the same lockout
cadence and reports valid credentials; any URL returning a
`www-authenticate` header can serve as the target.

### Refresh the year in a password list

Age the bundled common-password lists to the current year before spraying.

```
spray.sh -passupdate passwords.txt ExampleCorp
```

Outcome: rewrites `passwords.txt`, changing embedded years to the current one
and optionally appending company-name variants (`ExampleCorp2026`,
`ExampleCorpWinter2026`-style), so seasonal corporate patterns stay in the
candidate set.

### Generate a username list from name lists

Build the target user list from first and last name files.

```
spray.sh -genusers english-first-1000.txt english-last-1000.txt "<fn>.<ln>"
```

Outcome: emits every username matching the template — `<fn>.<ln>` yields
`john.smith`-style names, `"<fi><ln>"` yields `jsmith` — from the supplied
name lists, ready to use as the `users.txt` input for any spray mode.
