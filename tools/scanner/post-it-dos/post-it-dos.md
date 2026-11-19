---
name: "post-it-dos"
gentoo-category: "net-analyzer"
version: "9999"
homepage: "http://code.google.com/p/post-it-dos/"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: python
license: GPL-3
meta-checked: "2026-11-09"
github-repo: "TheProjecter/post-it-dos"
github-stars: 0
github-pushed: "2015-10-08"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# post-it-dos

> POST-it — a Python 2 proof-of-concept for slow HTTP POST denial-of-service that trickles large request bodies over hundreds of keep-alive connections.

## What it's for

POST-it (v1.5.0, by x41 of insecurety.net) demonstrates how a web server's worker pool is exhausted by slow POST bodies: each of its 500 threads opens a TCP connection, announces a multi-kilobyte `Content-Length`, then feeds the body one character at a time with long pauses, holding the connection open. The same pattern is available with GET requests. It exists to prove this vulnerability class (the family of slowloris/R.U.D.Y.-style resource exhaustion) on hardware you are authorized to test; there is no reporting, tuning, or rate limiting — it floods until stopped.

## Typical use cases

### Demonstrate slow-POST resource exhaustion

Tie up a lab web server's workers with body-trickling connections.

```
python2 postit.py -t post http://target.example.com
```

Outcome: 500 threads each connect to the target on port 80 with a rotating User-Agent (from `ualist.txt`), send POST headers announcing a ~6000-byte random urlencoded body, and dribble the body out with ~60-second gaps; the server's workers stay occupied until you interrupt the script (Ctrl-C sets the kill flag; the threads may need the terminal killed outright).

### Run the GET-flavoured variant

Show the same exhaustion using GET requests.

```
python2 postit.py -t get http://target.example.com
```

Outcome: identical thread and connection behavior with the request method switched to GET, for servers that mishandle slow bodies on GET as well as POST.

*No corpus evidence — derived from upstream source (`postit.py`; usage text and thread behavior read directly in the script, mirrored at TheProjecter/post-it-dos).*
