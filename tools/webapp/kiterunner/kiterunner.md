---
name: "kiterunner"
categories:
  - blackarch-webapp
version: "19.7d5824c"
homepage: "https://github.com/assetnote/kiterunner"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2021-05-17"
build-system: go
license: AGPL3
meta-checked: "2026-11-09"
github-repo: "assetnote/kiterunner"
github-stars: 3301
github-pushed: "2026-07-10"
github-archived: false
github-issues: 52
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# kiterunner

> Assetnote's contextual content discovery tool — brute-forces modern API routes (method, path, parameters per request) at high speed instead of just files and folders.

## What it's for

kiterunner (upstream binary `kr`; packaged as `kiterunner`) attacks a gap classic directory bruters miss: modern framework applications (Flask, Rails, Express, Django, ...) define routes that expect specific HTTP methods, headers, parameters and values, and respond 404 to plain GET-wordlist probes. Kiterunner compiles a dataset of Swagger/OpenAPI-derived routes into `.kite` wordlists and requests each route the way the framework expects, finding live API endpoints that look absent otherwise. It also does traditional brute forcing with dirsearch-style wordlists, at very high concurrency. Use it after subdomain/host enumeration when the target is or might be an API; it produces route findings, not vulnerabilities. Development stopped in 2021, but the route wordlists remain uniquely useful.

## Typical use cases

### Scan hosts with a compiled route wordlist

The canonical API-route brute force.

```
kiterunner scan hosts.txt -w /usr/share/kiterunner/routes-small.kite -x 20 -j 100 --fail-status-codes 400,401,404,403,501,502,426,411
```

Outcome: for every host in the file, requests each route from the `.kite` wordlist with its expected method and parameters; hits (status codes outside the fail list) are printed with their path and method. The packaged copy ships `routes-small.kite` and `routes-large.kite` in `/usr/share/kiterunner/`; `-x` caps connections per host and `-j` parallel hosts.

### Use Assetnote's hosted wordlists without a local file

Pull wordlists straight from the CDN.

```
kiterunner scan target.example.com -A=apiroutes-210328:20000 -x 5 -j 100 --fail-status-codes 400,401,404,403,501,502,426,411
```

Outcome: `-A` selects an Assetnote wordlist by name (the `:20000` suffix limits it to the first N routes), so no local `.kite` file is needed; output and filtering behave exactly like the local-wordlist scan.

### Brute force with a plain wordlist

Traditional content discovery with extensions.

```
kiterunner brute https://target.example.com/subapp/ -w dirsearch.txt -x 20 -j 1 -exml,asp,aspx,ashx -D
```

Outcome: `brute` mode walks a normal wordlist instead of routes; `-D` enables dirsearch compatibility (expanding `%EXT%` placeholders) with `-e` supplying the extension list — the mode for classic file/dir hunting or migrating dirsearch workflows.

### Compile your own route wordlist

Turn a Swagger spec into a `.kite`.

```
kiterunner kb compile routes.json routes.kite
```

Outcome: `kb compile` converts a JSON routes definition (e.g. exported from your target's own Swagger/OpenAPI disclosure) into the compiled `.kite` format the `scan` mode consumes — the way to fingerprint an application's exact API surface.

No corpus evidence — derived from upstream documentation (assetnote/kiterunner readme: quick start, CLI help, wordlist section; packaged wordlist paths from the BlackArch PKGBUILD).
