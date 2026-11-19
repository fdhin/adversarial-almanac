---
name: "teamsuserenum"
categories:
  - blackarch-recon
version: "v1.0.r1.g0c8b6c2"
homepage: "https://github.com/immunIT/TeamsUserEnum"
packaged-by: [blackarch]
blackarch-builddate: "2022-02-25"
build-system: go
license: GPL3
meta-checked: "2026-11-09"
github-repo: "immunIT/TeamsUserEnum"
github-stars: 177
github-pushed: "2021-07-09"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# teamsuserenum

> Enumerates which email addresses are registered in a Microsoft 365 tenant by abusing the Microsoft Teams API.

## What it's for

TeamsUserEnum by immunIT is a user-enumeration tool for Microsoft Teams. It uses the Teams search/presence API the way the desktop client does: for any email address you test, it reports whether that user exists in Teams, and for existing users it can additionally retrieve presence information and the device they connect from. Authentication is with a bearer token copied from an authenticated Teams web session (only the base64 JWT portion), which the tool replays against the API. This makes it a clean external-recon primitive for M365 tenants: validate harvested email lists, distinguish real mailboxes from decoys, and seed credible username lists — before any credential attack — with no packets sent at on-prem infrastructure. The binary is a Go program exposing the `userenum` subcommand.

## Typical use cases

### Validate a single email address

Check one candidate address against Teams.

```
teamsuserenum userenum -e user@example.com -t 'eyJ0eXAiOiJKV1Qi...pXVCJ9'
```

Outcome: queries the Teams API with the supplied bearer token and prints whether user@example.com is a registered Teams user, plus its presence if it exists.

### Enumerate a list of addresses

Feed a harvested address list and see which are real.

```
teamsuserenum userenum -f emails.txt -t 'eyJ0eXAiOiJKV1Qi...pXVCJ9' -v
```

Outcome: validates every address in emails.txt; existing users are reported with presence details, and verbose mode additionally shows the device each user connects from — the output is a confirmed, high-confidence username list for the tenant.

*No corpus evidence — derived from upstream documentation (flags verified against the repository's usage help; the README's own `-u` example is stale — the file-input flag is `-f`).*
