---
name: "pappy-proxy"
categories:
  - blackarch-webapp
version: "77.e1bb049"
homepage: "https://github.com/roglew/pappy-proxy"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "roglew/pappy-proxy"
github-stars: 411
github-pushed: "2018-11-20"
github-archived: false
github-issues: 14
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pappy-proxy

> Console-driven intercepting proxy for web application testing — Burp-style history, interception, and repeater in a terminal.

## What it's for

The Pappy Proxy records browser traffic through a local proxy and is driven entirely from an interactive console, with features explicitly modeled on Burp Suite. Requests live in a per-project history and can be filtered, viewed, edited in flight with your text editor, resent through a repeater, or replayed with macros; built-in decoder commands cover base64 and hex. To see HTTPS content, Pappy's generated CA certificate (`~/.pappy/certs/server.pem`) must be imported into the browser. The upstream has been dormant since 2018 and the packaged version runs on Python 2.

## Typical use cases

### Start a project session

```
mkdir test_project && cd test_project && pappy
```

Outcome: the proxy starts listening on 127.0.0.1:8080, and `data.db` plus `config.json` are created in the directory; traffic browsed through the proxy is recorded for the session.

### Run without dirtying a directory

```
pappy -l
```

Outcome: lite mode uses default settings and a temporary data file under /tmp that is deleted on quit.

### Inspect recorded history

```
pappy> ls a
pappy> vfq 42
```

Outcome: `ls a` lists the entire request history with per-request ids; `vfq <id>` (and `vfs <id>` for responses) prints the full stored message for review.

### Intercept and modify traffic in flight

```
pappy> ic req rsp
```

Outcome: requests and responses passing through the proxy are opened in your text editor before being forwarded, so headers, parameters, and bodies can be rewritten mid-test.

### Filter history to interesting requests

```
pappy> f inv method is GET
```

Outcome: sets the context to non-GET (state-changing) requests; the same filter language (`field comparer value`, e.g. `param ct csrf`, negated with `inv`) narrows history by body, headers, cookies, or parameters, and `scope_save` can pin it as the project's scope.

### Encode or decode a string

```
pappy> b64e "Hello World!"
```

Outcome: prints the base64-encoded string (`SGVsbG8gV29ybGQh`) and copies it to the clipboard; `b64d` decodes the clipboard contents back, showing a hexdump when the result is not printable.

No corpus evidence — derived from upstream documentation.
