---
name: "slowloris"
categories:
  - blackarch-dos
version: "0.7"
homepage: "http://ha.ckers.org/slowloris/"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "safesonic/slowloris.pl"
github-stars: 0
github-pushed: "2013-11-05"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-09"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# slowloris

> The original Perl slowloris: exhausts an HTTP server's connection slots by dribbling partial headers at minimal bandwidth.

## What it's for

slowloris.pl is RSnake's original Perl tool (v0.7, from ha.ckers.org) demonstrating HTTP connection-exhaustion: it opens TCP connections to a web server, sends partial request headers, and keeps sending just enough of a header at intervals that the connections never complete yet never time out. Thread-per-connection servers (classic Apache setups most famously) hold one slot per dangling request, so a single low-bandwidth machine can lock out legitimate visitors. It exists to let administrators test whether their configuration is vulnerable before an attacker does the same. The same script exists as an IPv6 build packaged separately as [[slowloris6]]; independent reimplementations of the technique are [[slowloris-py]] (Python 3, pip-installable) and [[pwnloris]] (Python with Tor routing).

## Typical use cases

### Run the basic attack

Open and hold slow connections against a target site.

```
perl slowloris.pl -dns example.com
```

Outcome: sockets are opened to example.com (default port 80), each sending partial headers and topping up periodically — connections default to a 100-second send timeout and 5-second TCP connect timeout; run it until the server stops answering new clients, then Ctrl-C to release everything.

### Size the connection count and timeouts explicitly

Control how many dangling connections are held.

```
perl slowloris.pl -dns example.com -port 8080 -num 200 -tcpto 5 -timeout 100
```

Outcome: holds up to 200 simultaneous partial connections (`-num`) to port 8080 (`-port`) with the given connect (`-tcpto`, default 5) and send (`-timeout`, default 100) timeouts in seconds — watch server-side connection counters climb to the configured cap.

### Check whether a server is vulnerable first

Run the built-in test before committing to a full run.

```
perl slowloris.pl -dns example.com -test
```

Outcome: the tool opens probe connections and times how long the server tolerates incomplete ones (announcing it can take up to ~14 minutes), reporting the timeouts to use — a quick pass/fail signal for whether hardening (e.g. request timeouts, reverse proxy) is in place.

### Attack an Apache httpready front end

Switch the request method to get past Apache's AcceptFilter buffering.

```
perl slowloris.pl -dns example.com -httpready
```

Outcome: requests use POST instead of GET (`-httpready` flips the method), the documented workaround for Apache's `httpready` accept filter, which otherwise lets the kernel buffer the incomplete GET until timeout.

### Test a TLS listener

Hold connections open over HTTPS.

```
perl slowloris.pl -dns example.com -https -port 443
```

Outcome: the same partial-header pattern runs over SSL/TLS (requires the perl IO::Socket::SSL module) against port 443, where each dangling connection additionally pins a TLS session on the server.

*No corpus evidence — derived from upstream documentation.*
