---
name: "device-pharmer"
categories:
  - blackarch-cracker
version: "40.b06a460"
homepage: "https://github.com/DanMcInerney/device-pharmer"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "DanMcInerney/device-pharmer"
github-stars: 149
github-pushed: "2021-01-30"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# device-pharmer

> Mass-opens Shodan results, IP ranges, domains or IP lists and optionally attempts a login against every responding page.

*No corpus evidence — derived from upstream documentation.*

## What it's for

device-pharmer (Dan McInerney, Python 2) answers two questions across thousands of hosts at once: what is running there, and do these credentials work? It concurrently requests every target (default 1,000 in flight, 15-second timeout), prints the HTTP status and page title of each responder, and when given `-u`/`-p` tries to log in to each page — form login first, then HTTP Basic Auth. Supplying `-f` with a string that only appears on the post-login page turns the run into a mass credential check. Every responder is logged to `<search-term-or-target>_results.txt`. It is HTTP(S)-only: no awareness of SSH, SNMP or other services, and no password generation beyond the pair you hand it.

## Typical use cases

### Enumerate Shodan search results

Pull device search results from Shodan and report which hosts answer with a page.

```
device-pharmer -s 'dir-300' -a SHODAN_API_KEY
```

Outcome: prints each result's IP plus the title of its page (redirects followed) and appends all responders to `dir-300_results.txt`. A free Shodan API key yields only the first 100 hosts; add `-n 5` to walk 5 pages (500 hosts) with a professional key.

### Validate one credential pair across many devices

Use a string that only appears after login to confirm where `admin:password` works.

```
device-pharmer -s 'dir-300' -a SHODAN_API_KEY -u admin -p password -f 'AdvancedRouting' -n 5
```

Outcome: for every responding host the tool attempts form login, then HTTP Basic; on hosts whose response HTML contains the marker it prints `* MATCH *` with IP and title, and the match is written to the results log. Form logins that answer with JSON instead of HTML can often be judged with `-f 'success'`.

### Sweep an IP range for responding pages

Point it straight at a range instead of Shodan.

```
device-pharmer -t 198.51.100.1-254 -c 100
```

Outcome: opens every address in the range (`198.51.100.1-254` and multi-octet `192.0.2-3.1-100` styles both expand), printing status and title per responder with concurrency capped at 100 requests at a time; responders are collected in a `_results.txt` log for the run.

### Test every IP in a file

Feed a prepared target list.

```
device-pharmer --ipfile targets.txt
```

Outcome: reads newline-separated IPs from `targets.txt` and treats them exactly like a range sweep — one status/title line per responder, results written to the run's results file.

### Probe a single HTTPS login

Check one known target over TLS with explicit credentials.

```
device-pharmer -t target.example.com/login -ssl -u user -p 'password' -f 'welcome-back'
```

Outcome: forces HTTPS for the request, attempts the login, and reports whether the marker string appears in the response — a quick yes/no credential test with the same output and logging as the bulk runs. All modes also accept `--proxy user:pass@ATTACKER_PROXY:8080` and `--timeout SECONDS`.
