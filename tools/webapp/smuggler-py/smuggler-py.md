---
name: "smuggler-py"
categories:
  - blackarch-webapp
version: "v2.0.0.r2.ge4b0dfa"
homepage: "https://github.com/gwen001/pentest-tools"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-14"
build-system: scripts
license: custom:IDBIL
meta-checked: "2026-11-09"
github-repo: "gwen001/pentest-tools"
github-stars: 3352
github-pushed: "2026-10-18"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 8
gh-activity-checked: "2026-11-09"
---

# smuggler-py

> gwen001's list-oriented HTTP request-smuggling tester: mass-probe hosts or URLs for CL.TE / TE.CL desyncs (v2.0.0).

## What it's for

This is `smuggler.py` from gwen001's pentest-tools collection (packaged here as `smuggler-py`),
built on James Kettle's request-smuggling research. It works from lists: give it a file of hosts
or of URLs and it fires a battery of Content-Length / Transfer-Encoding mutation probes at each,
multithreaded, and reports which targets look vulnerable. Scheme and HTTP methods are selectable
so the same list can be retried under different verb/protocol combinations. Upstream note: the
script was removed from the collection's master branch in October 2026 — this note documents the
packaged v2.0.0 interface. For single-target interactive testing see [[smuggler]] (defparam),
which covers the same vulnerability class with a different workflow.

## Typical use cases

### Probe a list of URLs

```
smuggler-py -u urls.txt
```

Outcome: every URL in the list is attacked with smuggling mutations across both schemes;
potentially vulnerable targets are printed (default verbosity shows only vulnerable results —
`-v 2` shows all requests, `-v 4` full debug).

### Probe a list of hosts across schemes

```
smuggler-py -o hosts.txt -s https -t 20
```

Outcome: each bare host is tested over HTTPS only with 20 threads (default 10), letting you
re-check a large host inventory under a single protocol quickly.

### Narrow the verbs and add headers

```
smuggler-py -u urls.txt -m POST,GET -d "X-Api-Version: 1" -i 15
```

Outcome: only the POST and GET variants of the mutations are tried (`-m`, default tries all
methods), the custom header is attached to every request (`-d`, repeatable), and the socket
timeout is raised to 15s (default 10) for slow origin servers.

No corpus evidence — derived from the packaged upstream source (smuggler.py at v2.0.0, commit e4b0dfa).
