---
name: "monsoon"
categories:
  - blackarch-webapp
version: "261.f4f9852"
homepage: "https://github.com/RedTeamPentesting/monsoon"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2021-04-27"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "RedTeamPentesting/monsoon"
github-stars: 500
github-pushed: "2026-07-17"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 36
gh-activity-checked: "2026-11-09"
---

# monsoon

> Fast HTTP enumerator that filters and displays responses in real time.

## What it's for

monsoon (RedTeamPentesting, Go) constructs HTTP requests from a wordlist, numeric range, or generating script, sends them concurrently, and filters the responses live — hiding or showing by status code, body size, and response-content patterns. The `fuzz` placeholder `FUZZ` marks where each input value lands: in the URL, POST data, a header, or HTTP basic auth, which makes it equally suited to content discovery and credential or session-ID brute-forcing. Companion subcommands round it out: `show` renders the request a pattern would produce, `list` re-filters the results of previous runs, and `test` executes request templates. Wordlists from the SecLists project plug in directly.

## Typical use cases

### Discover content while hiding the noise

```
monsoon fuzz --file filenames.txt --hide-status 200,404 https://example.com/FUZZ
```

Outcome: replaces `FUZZ` with each entry of `filenames.txt` and prints only responses whose status is neither 200 nor 404 — typically redirects, errors, and hidden endpoints, each shown live as it arrives.

### Brute-force a login form

```
monsoon fuzz --file passwords.txt --method POST --data 'username=admin&password=FUZZ' --hide-status 403 https://example.com/login
```

Outcome: posts each password for the fixed username and suppresses the failed-login status (403), so surviving lines are candidate valid credentials.

### Fuzz a header with a numeric range

```
monsoon fuzz --range 1-500 --header 'Cookie: sessionid=FUZZ' --hide-status 500 https://example.com/login
```

Outcome: generates every value in the range (comma-separated ranges and descending ranges like `10000-1` work too, with `--range-format '%010d'` for zero padding) and inserts it into the cookie, reporting non-error responses.

### Extract data from the hits

```
monsoon fuzz --file filenames.txt --extract '(?is)<title>(.*)</title>' https://example.com/FUZZ
```

Outcome: for every response, the regular expression's capture group is printed instead of the whole body — a compact answer column; `--show-pattern`/`--hide-pattern` filter on un-captured matches.

### Preview and re-filter runs

```
monsoon show --data 'username=admin&password=FUZZ' --method POST https://example.com/login
monsoon list
```

Outcome: `show` prints the exact HTTP request the pattern produces (catch template mistakes before sending anything), and `list` re-opens previous runs to apply different filters to already-collected responses.

No corpus evidence — derived from upstream documentation and the packaged source's built-in examples.
