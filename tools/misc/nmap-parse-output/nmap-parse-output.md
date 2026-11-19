---
name: "nmap-parse-output"
categories:
  - blackarch-misc
version: "26.a8e31b3"
homepage: "https://github.com/ernw/nmap-parse-output"
packaged-by: [blackarch, aur]
blackarch-builddate: "2023-07-10"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "ernw/nmap-parse-output"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-09"
worth: 3.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-stars: 557
github-pushed: "2024-04-29"
github-archived: false
github-issues: 7
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# nmap-parse-output

> Converts, manipulates, and extracts data from nmap XML scan output through a set of composable XSLT commands.

## What it's for

Post-processing `nmap -oX` results without hand-writing XPath: pulling host, port, service, product, and TLS lists out of large scans; filtering scans to (or excluding) specific hosts and ports; annotating results with comments and colors; and rendering HTML or JSON reports. Each subcommand is an XSLT stylesheet driven by a small bash wrapper (requires `xsltproc`); manipulation commands emit nmap XML again, so they chain through pipes with `-` standing in for stdin. `-u/--unfinished-scan` reads output from a scan that was interrupted partway. New commands can be added as XSLT files without touching the core script.

## Typical use cases

### Generate an HTML report

Turn a finished scan into a shareable report.

```
nmap-parse-output scan.xml html > scan.html
```

Outcome: converts the XML into a standalone HTML report; `html-bootstrap` produces a fancier Bootstrap-styled variant.

### List all HTTP(S) endpoints

Collect web attack surface from a scan.

```
nmap-parse-output scan.xml http-ports
```

Outcome: prints one `http://host:port` or `https://host:port` URL per line for every port detected as HTTP(S) — by service name (http, https, http-alt, http-proxy, rtsp, sip, ...) or by http-methods script output — preferring HTTPS where both protocols answer.

### Find hosts running a specific service

Pivot from service names to host:port lists.

```
nmap-parse-output scan.xml service-names
nmap-parse-output scan.xml service http-proxy
```

Outcome: the first command lists every service name found on open ports; feeding one back to `service` prints each matching endpoint in host:port form, ready to feed a proxy scanner or browser.

### Filter a scan and extract from the remainder

Narrow a big scan before deriving lists from it.

```
nmap-parse-output scan.xml exclude '198.51.100.10,198.51.100.20' | nmap-parse-output - ports
```

Outcome: removes the two named hosts from the XML, then prints the comma-separated open-port list (`22,80,443`) of everything that remains; `-` reads the piped XML on stdin, so any extract or manipulate command can be chained this way (`include`, `include-ports`, and `exclude-ports` filter by host or host:port the same way).

### Group hosts by open ports

Get a human-readable overview of a large scan.

```
nmap-parse-output scan.xml group-by-ports
```

Outcome: prints a markdown list grouping hosts (with hostnames where reverse DNS resolved them) by identical open TCP port sets, including a "no open TCP ports" group.

### Annotate a scan and report it

Record triage notes directly in the scan file.

```
nmap-parse-output scan.xml comment-ports '8080,198.51.100.10:443' 'should be filtered' \
  | nmap-parse-output - mark-ports '8080,198.51.100.10:443' red \
  | nmap-parse-output - comment-hosts '198.51.100.10' 'review further' \
  | nmap-parse-output - html > report.html
```

Outcome: embeds port and host comments plus red port highlighting into the XML and renders the annotated HTML report; `show-comments` on the resulting file reads the notes back.

*No corpus evidence — derived from upstream documentation.*
