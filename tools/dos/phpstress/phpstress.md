---
name: "phpstress"
categories:
  - blackarch-dos
version: "5.f987a7e"
homepage: "https://github.com/nightlionsecurity/phpstress"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "nightlionsecurity/phpstress"
github-stars: 40
github-pushed: "2014-05-17"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# phpstress

> PHP proof-of-concept that exhausts CPU and RAM on PHP-FPM/PHP-CGI fronted web servers using ordinary HTTP requests.

## What it's for

phpstress (Night Lion Security) is a PHP CLI script that stress-tests sites whose dynamic PHP content is handled by PHP-CGI or PHP-FPM under Apache or NGINX — including WordPress. It repeatedly requests a resource-intensive page, randomizing the query string each time (the `%r` token) so caches and identical-URL shortcuts don't help, while pacing connection and request delays. The effect is worker exhaustion: PHP processes pile up CPU and memory, and because of default timeouts the server stays loaded well after the script stops. The `%r` placeholder is what defeats naive caching — every request looks unique. It targets only infrastructure you are authorized to test.

## Typical use cases

### Run the basic attack

Flood a resource-intensive page with randomized query strings.

```
php phpstress.php http://example.com/search/?q=%r%
```

Outcome: the script opens connections to example.com and keeps requesting /search/ with a fresh random value in place of `%r` on every request, using the built-in defaults; server-side PHP worker utilization climbs while it runs.

### Tune every pacing knob

Control request counts, reuse, and delays explicitly.

```
php phpstress.php http://example.com/?r=%r% -m 1000000000 -k Y -c 150 -d 0.5 -r 0.01
```

Outcome: runs with maximum requests (`-m`), keep-alive checking enabled (`-k Y`), up to 150 requests per connection (`-c`), 0.5 s between new connections (`-d`), and 0.01 s between requests on a connection (`-r`) — the full parameter set, matching the documented defaults.

### Hit a heavy endpoint at a custom rate

Slow the attack down to observe server behavior under sustained, lower-intensity load.

```
php phpstress.php example.com/wp-content/?r=%r% -c 50 -d 2 -r 0.5
```

Outcome: each connection issues up to 50 requests with 2 s between connections and 0.5 s between requests — a gentler profile suited to watching worker pool and memory growth in monitoring while the test runs.

*No corpus evidence — derived from upstream documentation.*
