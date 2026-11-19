---
name: "jstillery"
categories:
  - blackarch-webapp
version: "65.512e9af"
homepage: "https://github.com/mindedsecurity/JStillery"
packaged-by: [blackarch]
blackarch-builddate: "2019-04-22"
build-system: node
license: GPL3
meta-checked: "2026-11-09"
github-repo: "mindedsecurity/JStillery"
github-stars: 899
github-pushed: "2019-05-30"
github-archived: false
github-issues: 22
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# jstillery

> Advanced JavaScript deobfuscation via partial evaluation — rewrites obfuscated scripts into readable source from the CLI, a web UI or a REST endpoint.

## What it's for

JStillery (MindedSecurity) deobfuscates JavaScript using AST-based partial evaluation: instead of only un-nesting packers cosmetically, it executes the safe parts of the code (string arithmetic, `String.fromCharCode` chains, array rotations) and emits the equivalent simplified source. Reach for it when you hit eval-packer or heavy string-obfuscation output during web recon and need readable code to audit for endpoints, secrets or client-side logic. It offers three surfaces: a CLI (`jstillery` on the packaged system) for files and stdin, a local web server with a browser UI, and a REST API on the same server for piping deobfuscation into scripts. Upstream has been dormant since 2019, so modern ES2020+ syntax may exceed what it evaluates.

## Typical use cases

### Deobfuscate a JavaScript file

The straightforward single-file run.

```
jstillery obfuscated.js > readable.js
```

Outcome: prints the partially-evaluated, deobfuscated source to stdout (redirect to keep it); compare against the original to see which string and structure tricks were undone.

### Deobfuscate from a pipe

Pull a script straight off a target and clean it in one line.

```
curl -s http://target.example.com/static/app.js | jstillery
```

Outcome: the fetched script is read from stdin and the deobfuscated output is printed to stdout — no intermediate file needed.

### Deobfuscate through the REST API

Use the server mode for scripted or repeated calls.

```
npm run start_server   # from a source checkout, after `npm run build_server`
curl 'http://localhost:3001/deobfuscate' -d '{"source":"a= String.fromCharCode(41);b=a"}' -H 'Content-type: application/json'
```

Outcome: the server (web UI at `http://0:3001/`, paste code and press Ctrl-Enter) also answers `POST /deobfuscate` with a JSON body like `{"source":"a = 1;"}` — the cleaned code comes back in the `source` field, ready for automated pipelines. Configuration lives in `server/config_server.json` in the checkout.

No corpus evidence — derived from upstream documentation (mindedsecurity/JStillery README: CLI, stdin, server and REST sections).
