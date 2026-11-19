---
name: "ldapenum"
categories:
  - blackarch-recon
version: "0.1"
homepage: "https://gobag.googlecode.com/svn-history/r2/trunk/ldap/ldapenum/"
packaged-by: [blackarch]
blackarch-builddate: "2019-05-10"
build-system: perl
license: GPL2
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# ldapenum

> Perl-era LDAP enumerator that pulls system and password information from Windows domain controllers when SMB null sessions (IPC$) are locked down.

## What it's for

ldapenum is a small Perl script (SourceForge project, 2005-era, version 0.03 Alpha) designed to enumerate system and password information from domain controllers through the LDAP service "when IPC$ is locked" — that is, when classic null-session enumeration over SMB is blocked but the LDAP service still accepts connections. It was tested on Windows and Linux and depends on the perl-ldap module family; BlackArch installs it as `/usr/bin/ldapenum`. It predates modern Active Directory enumeration by two decades: for current domains use ldeep, windapsearch or nmap's `ldap-search` script instead. Its exact command-line flags cannot be verified — the original homepage, the perl source host and the SourceForge download are all dead — so this note is description-only.

*No corpus evidence; no recoverable upstream documentation — description only.*
