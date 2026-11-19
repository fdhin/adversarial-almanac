---
name: "smbsr"
categories:
  - blackarch-scanner
version: "50.7f86241"
homepage: "https://github.com/oldboy21/SMBSR"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: Apache
meta-checked: "2026-11-09"
github-repo: "oldboy21/SMBSR"
github-stars: 149
github-pushed: "2023-06-16"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# smbsr

> SMB share crawler that reads the contents of files across whole subnets hunting for secrets, keywords, and interesting filenames.

## What it's for

SMBSR (by oldboy21, python3; the name expands to SMB Search & Retrieve)
goes where name-based spiders stop: given a CIDR, IP, host file, or
hostname list it finds SMB listeners (masscan module optional), attempts
authentication, and then recursively visits every folder and share it can,
opening files and searching their contents. It parses an unusually wide
set of formats — office documents, PDFs, emails, even images and audio
via OCR/speech — and flags hits by content keywords, filename patterns,
regular expressions, or extension, recording everything in a local SQLite
database and optionally CSV. It can also pull the computer list straight
from Active Directory via LDAP. Note the project is unmaintained: its
README directs users to the merged successor, SMBAT. One flag caveat:
the README's `-file-extensions` example is stale — the packaged source
implements `-file-interesting` and `-file-extensions-black`.

## Typical use cases

### Crawl one host's shares for keyword hits

Content search with a wordlist of secrets.

```
smbsr -IP 198.51.100.10 -username user -password Password1 -domain EXAMPLE -word-list-path tomatch.txt -multithread -max-size 1000 -T 2
```

Outcome: authenticates, walks every accessible share recursively, and
searches the parsed contents of files up to 1000 bytes against every term
in tomatch.txt; matches land in the SQLite database (file, share, IP,
position, timestamps) and print live, ready to re-fetch the file.

### Discover and crawl a whole subnet

Let masscan find the SMB hosts first.

```
smbsr -IP 198.51.100.0/24 -masscan -username user -password Password1 -domain EXAMPLE -word-list-path tomatch.txt -csv
```

Outcome: the /24 is port-scanned for 445 via the masscan module, each
responder gets the authenticated crawl, and `-csv` additionally exports
the interesting-file inventory to CSV for the report.

### Hunt interesting file types with bounded depth

Filename-and-extension triage without reading everything.

```
smbsr -ip-list-path hosts.txt -file-interesting ppk,kdbx,sqlite -depth 5 -T 20 -multithread
```

Outcome: every host in the file is crawled only to depth five, with files
whose extensions (or names) match the interesting list collected into the
database — a fast pass for key files, password databases, and credential
stores before any content search.

### Enumerate targets from Active Directory

Let the domain hand you the computer list.

```
smbsr -ldap -dc-ip 198.51.100.10 -domain EXAMPLE -username user -password Password1 -word-list-path tomatch.txt
```

Outcome: the same credentials double as an LDAP bind against the domain
controller, the computer-object list is pulled as the target set, and the
crawl proceeds over the domain's actual SMB estate instead of a guessed
range.

*No corpus evidence — derived from upstream documentation (README; flags verified against the packaged source's argparse).*
