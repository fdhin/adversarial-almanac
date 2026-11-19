---
name: "apache-users"
categories:
  - blackarch-scanner
version: "2.1"
homepage: "https://labs.portcullis.co.uk/downloads/"
packaged-by: [blackarch, kali, nixpkgs]
blackarch-builddate: "2025-07-18"
build-system: perl
license: GPL2
meta-checked: "2026-11-09"
github-repo: "CiscoCXSecurity/apache-users"
github-stars: 1
github-pushed: "2021-04-10"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# apache-users

> Perl scanner that enumerates valid system usernames through Apache's UserDir (`~user`) module.

## What it's for

apache-users (Andy at Portcullis, 2008; new code base derived from `apache.pl` v1.0 by
Doc) probes a web server's per-user directories: for every candidate name it requests
`/~name/` and reports the name when the HTTP status line matches a configured response
code, enumerating valid local accounts on the box behind Apache whenever `mod_userdir` is
enabled. It threads over a dictionary (a bundled 8,913-entry `names` file installs to
`/usr/share/apache-users/names`), supports SSL, and lets you tune the match code since
servers answer differently. The output is a username list to feed SSH/FTP brute-forcers or
further enumeration — it does not test passwords or any other service.

## Typical use cases

### Enumerate users with the bundled wordlist

The standard run against a UserDir-enabled server.

```
apache-users -h 198.51.100.10 -p 80 -l /usr/share/apache-users/names -e 403 -t 20
```

Outcome: requests `/~<name>/` for each dictionary entry with 20 threads and prints every
name whose response code matches `-e` (default 403) as `name exists on <host>`, plus a
runtime summary. Set `-e` to the code your target returns for existing users — e.g. 200
when user pages load normally.

### Enumerate over HTTPS with a custom list

Adapt to TLS targets and a tailored candidate set.

```
apache-users -h target.example.com -p 443 -s 1 -l custom_names.txt -e 200 -t 10
```

Outcome: same check over TLS (`-s 1`), matching names whose `~` directory responds 200,
yielding the valid-username set for the host.

*No corpus evidence — derived from upstream source (Cisco CX Security mirror of the Portcullis original; flags verified against `getopt`/usage in `apache2.pl`).*
