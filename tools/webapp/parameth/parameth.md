---
name: "parameth"
categories:
  - blackarch-webapp
version: "56.8da6f27"
homepage: "https://github.com/mak-/parameth"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknonw
meta-checked: "2026-11-09"
github-repo: "mak-/parameth"
github-stars: 1394
github-pushed: "2019-08-24"
github-archived: true
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# parameth

> Brute-discovers hidden GET and POST parameters on a web endpoint by diffing responses.

## What it's for

parameth answers a narrow recon question: which parameter names does a script accept? Given a URL it tries a wordlist of candidate parameter names as GET and POST parameters and flags names that change the response — a different status, size, or content — since an accepted parameter usually alters one of those. It copes with dynamic pages through a variance/offset setting and can ignore given status codes or response sizes. A Python 2 era tool whose upstream is archived; for a Python 3 successor built on the same idea see [[parampampam]].

## Typical use cases

### Discover accepted parameters on a script

```
parameth -u "http://target.example.com/test.php"
```

Outcome: fuzzes GET and POST parameter names against the URL and lists the candidates whose responses deviate from the baseline — parameters the application likely processes.

### Tune out dynamic-page noise

```
parameth -u "http://target.example.com/app.php" -off 5
```

Outcome: response-size differences within the offset are treated as noise, so alternating page sizes (rotating banners, counters) stop producing false positives.

### Use a custom parameter wordlist

```
parameth -u "http://target.example.com/app.php" -p params.txt
```

Outcome: the provided file's parameter names are tried instead of the bundled list — for example names harvested from PHP source by grepping `$_GET`/`$_POST` occurrences.

### Fuzz a POST body directly

```
parameth -u "http://target.example.com/app.php" -d "id=1"
```

Outcome: the given string is used as the default POST data while candidate parameter names are added, focusing discovery on parameters accepted in the request body.

No corpus evidence — derived from upstream documentation.
