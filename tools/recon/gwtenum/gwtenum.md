---
name: "gwtenum"
categories:
  - blackarch-recon
version: "7.f27a5aa"
homepage: "http://www.gdssecurity.com/l/t/d.php?k=GwtEnum"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: CCPL
meta-checked: "2026-11-09"
github-repo: "GDSSecurity/GWT-Penetration-Testing-Toolset"
github-match: "exact"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-stars: 237
github-pushed: "2020-01-21"
github-archived: false
github-issues: 8
gh-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# gwtenum

> Enumerate the RPC methods a Google Web Toolkit application hides in its nocache.js bootstrap.

## What it's for

GwtEnum parses a GWT application's entrypoint JavaScript (`*.nocache.js`),
resolves the serialization policy files it references and decodes them into
the list of RPC service methods the client can call — the GWT equivalent of
a WSDL dump, giving you the full method surface before crafting requests
against the server's RPC endpoints. The practical hurdles are covered by
options: route the fetch through an intercepting proxy (`-p`), reuse an
authenticated session's cookies (`-k`), and answer HTTP basic-auth prompts
(`-b`). It ships as part of GDS Security's GWT-Penetration-Testing-Toolset;
the same package also installs `gwtfuzzer` (request fuzzer) and `gwtparse`
(GWT serialization parser) as separate commands. The code is Python-2 era,
so it expects a Python 2 interpreter.

## Typical use cases

### Enumerate methods from the entrypoint script

```
gwtenum -u "https://target.example.com/app/app.nocache.js"
```

Outcome: downloads the bootstrap script, resolves the policy files it
references and prints each service method with its parameters — the full
callable RPC surface of the application.

### Enumerate through a proxy with an authenticated session

```
gwtenum -u "https://target.example.com/app/app.nocache.js" \
    -p "http://127.0.0.1:8080" -k "JSESSIONID=AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA"
```

Outcome: the same enumeration with every request visible in your proxy and
carrying the supplied session cookie, so protected GWT apps can be
enumerated under a valid logged-in session.

*No corpus evidence — derived from upstream documentation and source
(optparse flags read from gwtenum/gwtenum.py).*
