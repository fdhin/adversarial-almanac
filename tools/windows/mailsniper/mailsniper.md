---
name: "mailsniper"
categories:
  - windows
homepage: "https://github.com/dafthack/MailSniper"
github-repo: "dafthack/MailSniper"
github-match: "exact"
origin: "awesome-pentest"
recipe: "upstream"
license: MIT
github-stars: 3293
github-pushed: "2026-09-21"
github-commits-year: 5
gh-checked: "2026-11-14"
worth: 6.7
worth-basis: "full (activity+popularity+recency+adoption)"
packaged-by: []
---

# mailsniper

> Exchange post-exploitation toolkit in PowerShell — mailbox and GAL pillaging, OWA/EWS password spraying and username harvesting from a single front door.

## What it's for

MailSniper (dafthack / Black Hills InfoSec) is a PowerShell toolkit for attacking Microsoft Exchange from the outside: with one valid credential it searches mail for sensitive terms (a user's own mailbox via `Invoke-SelfSearch`, or *every* mailbox via `Invoke-GlobalMailSearch` after granting itself the ApplicationImpersonation role as Exchange admin), dumps the Global Address List through OWA/EWS, sprays passwords against OWA, EWS and even Gmail portals, harvests valid usernames and the AD domain from timing differences in OWA logins, and identifies which mailboxes the current user can read. Reach for it in the phase right after the first Exchange credential lands — the typical haul is more credentials, the full user list, and mailbox intel. It targets classic on-prem Exchange (2010-era defaults) and O365 with SSO variants; it is not a proxyshell/proxylogon-style unauthenticated exploit chain. PowerShell on Windows; dot-source the script and call the functions.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Search a compromised user's own mailbox

```
Invoke-SelfSearch -Mailbox user@example.com
```

Outcome: autodiscovers the Exchange server from the email address, connects over EWS as the current user, and searches the latest 100 inbox emails for the default terms `*pass*`, `*creds*`, `*credentials*` (`-Terms`, `-MailsPerUser`, `-Folder all` and `-Regex` to widen it); `-OutputCsv out.csv` saves hits.

### Search every mailbox in the org as Exchange admin

```
Invoke-GlobalMailSearch -ImpersonationAccount current-username -ExchHostname exchange.example.com -OutputCsv global-email-search.csv
```

Outcome: prompts for Exchange admin credentials, opens a PowerShell remoting session, grants `current-username` the ApplicationImpersonation role, lists all mailboxes, and searches each one over EWS for the default terms, writing matches to the CSV.

### Dump the Global Address List

```
Get-GlobalAddressList -ExchHostname mail.example.com -UserName EXAMPLE\user -Password 'Password123' -OutFile gal.txt
```

Outcome: logs into OWA and pulls the GAL via the FindPeople method (Exchange 2013+), falling back to EWS — the output file is the org's full email list for spraying, phishing and username mapping.

### Password-spray the OWA portal

```
Invoke-PasswordSprayOWA -ExchHostname mail.example.com -UserList .\userlist.txt -Password 'Password123' -Threads 15 -OutFile owa-sprayed-creds.txt
```

Outcome: sprays one password against every user in the list through Outlook Web Access, recording valid logins to the output file; `Invoke-PasswordSprayEWS` does the same over EWS and `Invoke-PasswordSprayGmail` against Gmail.

### Harvest valid usernames and the domain from OWA

```
Invoke-UsernameHarvestOWA -ExchHostname mail.example.com -UserList .\userlist.txt -Threads 1 -OutFile owa-valid-users.txt
```

Outcome: uses timing differences in OWA authentication responses to distinguish existing from non-existing accounts without any valid credentials; `Invoke-DomainHarvestOWA -ExchHostname mail.example.com` recovers the login domain from the WWW-Authenticate header.

### Find mailboxes the current user can already read

```
Invoke-OpenInboxFinder -EmailList email-list.txt
```

Outcome: walks the address list and reports every mailbox whose Inbox the current credential can open — misconfigured mailbox permissions surfaced as reading access.
