---
name: "shellinabox"
categories:
  - blackarch-backdoor
version: "428.98e6eeb"
homepage: "https://github.com/shellinabox/shellinabox"
packaged-by: [blackarch]
blackarch-builddate: "2017-02-08"
build-system: autotools
license: GPL-2.0-only
meta-checked: "2026-11-09"
github-repo: "shellinabox/shellinabox"
github-stars: 3087
github-pushed: "2025-09-06"
github-archived: false
github-issues: 229
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# shellinabox

> Web server (`shellinaboxd`) that exports a login shell or any command-line program to a browser-based VT100 terminal — no browser plugins, no client install.

## What it's for

Shell In A Box publishes command-line programs as an AJAX/JavaScript VT100 terminal reachable from any modern browser. With no arguments it wraps `/bin/login` (when run as root) or `ssh localhost` (unprivileged); the `-s` option can instead export an arbitrary command under a chosen URL path, which makes it a quick way to put an SSH jump point or a single CLI tool behind a URL. It is legitimate operations software, not offensive tooling — but a browser-reachable shell on an internal host is equally useful as post-compromise access, which is why it lands in backdoor catalogs. HTTPS is the default (self-signed certificates are auto-generated when openssl is available; `-t` disables SSL), and the default listen port is 4200.

No corpus evidence — derived from upstream documentation (the `shellinaboxd` manual in the project wiki).

## Typical use cases

### Publish a browser login shell on the default port

Get a shell on a remote box from a browser with zero client-side setup.

```
shellinaboxd
```

Outcome: serves a login prompt at https://localhost:4200/ and auto-redirects HTTP to HTTPS, generating a self-signed certificate if none exists; root invocations get `/bin/login`, unprivileged ones an `ssh localhost` session instead.

### Serve the terminal without TLS

Skip certificate handling on a trusted intranet or during testing.

```
shellinaboxd -t
```

Outcome: the same login shell is served on plain http://localhost:4200/ with the SSL upgrade disabled — no certificate warnings, no encryption.

### Point the web terminal at SSH on another host

Turn the box into a browser SSH jump point.

```
shellinaboxd -s /:SSH:target.example.com
```

Outcome: the terminal at https://localhost:4200/ prompts for SSH credentials and connects to target.example.com instead of running a local login.

### Export a fixed command under a URL path

Expose one specific program rather than a full shell.

```
shellinaboxd -t -s /:LOGIN -s /who:nobody:nogroup:/:w
```

Outcome: a normal login shell at http://localhost:4200/ plus a second service at /who that renders the output of `w` as user nobody:nogroup — the reference example of the `USER:GROUP:CWD:CMD` service syntax.

### Require interactive authentication for a custom shell

Have the browser collect credentials before launching a chosen program.

```
shellinaboxd -t -s /:AUTH:HOME:/bin/bash
```

Outcome: the browser prompts for username and password, then launches `/bin/bash` in the authenticated user's home directory; unprivileged users may run this, but then only that same user can log in.
