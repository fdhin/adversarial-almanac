---
name: "web2ldap"
categories:
  - blackarch-misc
version: "1.8.1"
homepage: "https://web2ldap.de/"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-16"
build-system: python
license: Apache-2.0
meta-checked: "2026-11-09"
github-repo: "gdevenyi/web2ldap"
github-stars: 0
github-pushed: "2024-07-03"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 1.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# web2ldap

> Full-featured LDAPv3 client delivered as a self-contained web application.

## What it's for

web2ldap is a generic LDAPv3 client that runs in the browser: start it
locally, open the web UI, and browse or modify any LDAP directory. It is
schema-aware with no assumptions about tree structure — entry editing,
complex search filters, compare, rename, password changes, LDIF
import/export, schema browsing and replication monitoring all work out of
the box without per-directory configuration. It is a client, not a server:
it connects to directories such as OpenLDAP or Active Directory over
LDAP/LDAPS/StartTLS. The packaged Python distribution installs a single
`web2ldap` command that starts a stand-alone web server; the web UI then
drives everything else.

## Typical use cases

### Start the built-in web server

```
web2ldap
```

Outcome: starts a stand-alone WSGI server on 127.0.0.1:1760; open http://127.0.0.1:1760/web2ldap/ in a browser and enter the directory host and bind DN to connect.

### Listen on a different port

```
web2ldap 8080
```

Outcome: same stand-alone server, bound to 127.0.0.1:8080 instead of the default 1760.

### Expose the client to other hosts

```
web2ldap 0.0.0.0 1760
```

Outcome: the server listens on all interfaces so the web client is reachable from another workstation — keep this on a trusted network, since the UI reaches your directory infrastructure.

*No corpus evidence — derived from upstream documentation.*

*GitHub: `gdevenyi/web2ldap` is a third-party source mirror of the real web2ldap tree (setup.py identical to the packaged 1.8.1; the mirror carries the later 1.8.2). Canonical upstream is the author's own site — web2ldap.de / git.stroeder.com — with releases on PyPI; stat fields predate this link and await recompute.*
