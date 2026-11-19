---
name: "mybff"
categories:
  - blackarch-cracker
version: "99.ba4de8d"
homepage: "https://github.com/MooseDojo/myBFF"
packaged-by: [blackarch]
blackarch-builddate: "2025-03-15"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "MooseDojo/myBFF"
github-stars: 147
github-pushed: "2025-03-04"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# mybff

> Modular web-application brute forcer that fingerprints the app, sprays credentials, then enumerates what the account can reach.

## What it's for

myBFF takes a host, a username list and a single password (or a password file), works out which web application is running, and applies a module that knows that application's login flow. After a successful login most modules keep going: Citrix Gateway and Okta enumerate authorized applications, Juniper Portal probes for 2FA bypass, Outlook/Office365 parses mailbox data, SMB reports which accounts are administrators, FTP lists the root directory. Modules cover HP SiteScope, Citrix Gateway, Juniper Portal, MobileIron, Outlook/O365, WordPress, CiscoVPN, Okta, Jenkins, SMB and FTP. It is a Python 2 era framework — running it today means providing a python2 runtime with its dependencies.

## Typical use cases

### Spray one password across a user list

Let the framework detect the application.

```
mybff --host https://target.example.com -U userfile.txt -p Password1
```

Outcome: fingerprints the application at the host, picks the matching module, tries the one password against every user in the list, then runs that module's post-login enumeration; findings print to stdout.

### Check which accounts are administrators over SMB

Use the smb:// scheme together with a domain.

```
mybff --host smb://198.51.100.10 -U userfile.txt -p Password1 --domain corp.example.com
```

Outcome: tests each credential pair over SMB and reports which valid accounts hold administrator rights; the SMB module requires `--domain` and a `smb://` host.

### Dry-run the module selection first

Skip password guessing to confirm what will fire.

```
mybff --host https://target.example.com -u user -p Password1 -d
```

Outcome: dry-run mode performs the module's non-guessing checks without submitting login attempts — useful to verify the right module was chosen before running a real spray.

### Attack an app on a virtual host and save output

Target a subdirectory-served app and write results to a file.

```
mybff --host https://target.example.com -U userfile.txt -P passwords.txt --vhost owa -o hits.txt
```

Outcome: `--vhost owa` selects the application served under `/owa` when the host root serves something else; the run's output is additionally written to `hits.txt`.

*No corpus evidence — derived from upstream documentation.*
