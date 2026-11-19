---
name: "octopwnweb"
categories:
  - blackarch-automation
version: "14.e0f83ee"
homepage: "https://github.com/skelsec/octopwnweb"
packaged-by: [blackarch]
blackarch-builddate: "2023-05-04"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "skelsec/octopwnweb"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# octopwnweb

> skelsec's internal-pentest framework compiled to WebAssembly (via Pyodide) so the whole toolkit runs client-side in a browser tab — no local Python install.

## What it's for

Running skelsec-style internal network attack tooling (the protocol/credential attack suite lineage of his tools) entirely in the browser: the Python framework is compiled to WASM with Pyodide and served as a static web page, so an operator can drive it from any machine with a browser. Status: abandoned — the upstream repository (skelsec/octopwnweb) has been deleted and no archived copy of its documentation survives, so what the in-browser UI exposed beyond the packaged description is no longer verifiable. What remains packaged is the static page and a launcher that serves it locally; the framework's capabilities should be treated as historical. Do not confuse with unrelated "octopwn"-named projects; this note tracks only the deleted skelsec original.

*No corpus evidence — upstream documentation is gone; entry point derived from BlackArch packaging.*

## Typical use cases

### Serve the WASM toolkit locally

```
octopwnweb
```

Outcome: starts a plain Python HTTP server (port 8000 by default) serving the packaged static page directory; open `http://localhost:8000` in a browser to load the WebAssembly build of the framework. Everything runs client-side — there is no server component to configure, and stopping the command with Ctrl-C takes the page's backend-less serving down with it.
