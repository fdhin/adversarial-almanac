---
name: "airpwn"
categories:
  - blackarch-wireless
version: "1.4"
homepage: "http://airpwn.sourceforge.net"
packaged-by: [blackarch]
blackarch-builddate: "2015-01-29"
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
github-repo: "grepwood/airpwn"
github-stars: 2
github-pushed: "2013-04-22"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# airpwn

> 802.11 injection framework that answers a client's request faster than the real server, so the client renders your payload.

## What it's for

Airpwn listens on a monitor-mode interface, matches intercepted TCP traffic against regex rules from a config file, and injects a crafted server-to-client response with correct sequence numbers and the AP's MAC — winning the race against the legitimate server (~1 ms vs ~50 ms), the client accepts the injected segment as the real reply. Its classic use is replacing HTTP images or page fragments on unencrypted networks; it also supports WEP networks by giving it the key. Limits to know: TCP only, server-to-client responses only, one card does both listening and injecting, and drivers must be supported by LORCON. It is a content-injection tool for assessing how clients handle unauthenticated wireless traffic — not a key cracker and not a general packet forger.

## Typical use cases

### Inject canned HTTP content with a shipped rule

Run airpwn against a monitor interface using one of the bundled configs.

```
sudo airpwn -i mon0 -c conf/greet_html -d madwifing
```

Outcome: airpwn tunes to the channel, watches matching `GET`/`POST` requests (the `greet_html` rule ignores image URLs) and injects `content/greet_html` before the server replies; associated clients display the injected HTML fragment appended ahead of the real page content.

### Write a minimal injection rule

Define your own match-and-response rule and run it.

```
printf 'begin hijack\nmatch ^GET [^ ]+\\.html\noption reset\nresponse content/airpwned_png\n' > hijack.conf
sudo airpwn -i mon0 -c hijack.conf -d hostap
```

Outcome: each rule block starts with `begin <name>`, applies `match`/`ignore` PCREs to the TCP payload, and names a `response` file whose bytes become the injected segment; `option reset` starts a fresh response stream. While running, airpwn prints each match it acts on.

### Scope the sniffer and keep a verbose log

Restrict processing to a BPF filter and record what airpwn does.

```
sudo airpwn -i mon0 -c hijack.conf -d prism54 -f "tcp port 80" -l airpwn.log
```

Outcome: only traffic passing the libpcap filter is evaluated (less CPU, less noise), and verbose logging of matches and injections is written to `airpwn.log` for review after the run.

*No corpus evidence — derived from upstream documentation.*
