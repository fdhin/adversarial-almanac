---
name: "o365enum"
categories:
  - blackarch-cracker
version: "19.522a54c"
homepage: "https://github.com/gremwell/o365enum"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "gremwell/o365enum"
github-stars: 307
github-pushed: "2024-05-02"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# o365enum

> Checks which usernames exist in a Microsoft 365 tenant via ActiveSync, Autodiscover, or the office.com login endpoint.

## What it's for

o365enum reads a user list and reports which accounts exist in an Office 365 tenant, as CSV — `0` invalid, `1` valid, `2` valid username with a valid password. Three detection methods: ActiveSync (default) sends Basic-auth probes to the `/Microsoft-Server-ActiveSync` endpoint and keys on the presence of the `X-MailboxGuid` response header, since the HTTP status code no longer distinguishes existing from missing users; Autodiscover distinguishes a 200 response (exists) from a 302 redirect (missing) without submitting any credentials; office.com reads the `IfExistsResult` field from login.microsoftonline.com, which burns no authentication attempt but is only reliable for pure Exchange Online tenants — hybrid or on-premises Exchange can report every name as existing. The `-p` password (default `Password1`) is only actually attempted by the ActiveSync method, and `-n` repeats each check to suppress false negatives.

## Typical use cases

### Enumerate users with the ActiveSync method

Run the default check with an explicit password.

```
o365enum -u users.txt -p Password2 -n 1 -m activesync
```

Outcome: prints a `username,valid` CSV header and one row per user (for example `existing@corp.example.com,1`); a `2` means the supplied password matched, so those credentials are immediately usable.

### Enumerate without submitting credentials

Use the Autodiscover endpoint instead.

```
o365enum -u users.txt -m autodiscover
```

Outcome: requests `/autodiscover/autodiscover.json` for each user and marks 200 responses as existing and 302 redirects as missing — no password is ever submitted, so no failed-authentication attempts are generated against the tenant.

### Screen a pure-cloud tenant through the login page

office.com method with extra retries.

```
o365enum -u users.txt -m office.com -n 5
```

Outcome: queries the GetCredentialType endpoint behind the office.com login and interprets `IfExistsResult` (0 = exists, 1 = missing); trust the results only for tenants without hybrid or on-premises Exchange, which can produce all-exists false positives.

*No corpus evidence — derived from upstream documentation.*
