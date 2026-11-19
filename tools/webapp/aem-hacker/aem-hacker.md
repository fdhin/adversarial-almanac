---
name: "aem-hacker"
categories:
  - windows
homepage: "https://github.com/0ang3el/aem-hacker"
origin: "howtohunt"
recipe: "upstream"
packaged-by: []
build-system: python
license: MIT
meta-checked: "2026-11-13"
github-repo: "0ang3el/aem-hacker"
github-match: "exact"
github-stars: 811
github-pushed: "2024-07-28"
github-issues: 15
gh-checked: "2026-11-13"
worth: 4.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
---

# aem-hacker

> Toolset for hacking Adobe Experience Manager: dispatcher-bypass scanning for exposed servlets, default credentials, SSRF and RCE paths across an AEM webapp.

## What it's for

AEM hacker (by Mikhail Egorov / @0ang3el) automates bug-hunting and
pentesting of Adobe Experience Manager webapps — an enterprise CMS whose
default servlets, consoles and dispatchers are notoriously often exposed.
`aem_hacker.py` scans one URL for dozens of known weaknesses: exposed
DefaultGetServlet/QueryBuilder/GQL servlets (JCR content and secrets
disclosure), POSTServlet and node-creation rights (stored XSS → RCE),
default passwords (`admin:admin`, `author:author`...), exposed Felix/CRXDE
consoles, WCMDebugFilter XSS (CVE-2016-7882), SSRF servlets
(SalesforceSecretServlet CVE-2018-5006, ReportingServicesServlet
CVE-2018-12809, SitecatalystServlet...) and WebDav/Groovy-console RCE
paths, attempting dispatcher bypasses throughout. Companion scripts cover
discovery (`aem_discoverer.py`) and turning detected SSRF into RCE
(`aem_ssrf2rce.py`, `aem_server.py`, `aem-rce-sling-script.sh`). It detects
and reports; every finding still needs manual verification.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Full scan with an SSRF callback host

Run every handler, including the ones needing back-connections.

```
python3 aem_hacker.py -u https://aem.example.com --host ATTACKER_IP
```

Outcome: all checks run against the instance with dispatcher-bypass
attempts; the SSRF handlers open a listener context on your host/port so
blind SSRF hits are observable — a VPS is required for SSRF detection.
`--host`/`--port` control the callback endpoint, `-H "Header: value"`
attaches extra headers, `--workers N` parallelizes.

### Run a read-only handler subset through Burp

Keep production targets inside read-only rules of engagement.

```
python3 aem_hacker.py -u https://aem.example.com --proxy 127.0.0.1:8080 --handler get_servlet --handler felix_console
```

Outcome: only the named handlers execute (no write or SSRF checks), and all
traffic flows through the proxy for response-by-response review.
`--listhandlers` prints every available handler name before you commit to a
selection.

### Discover AEM instances from a URL list

Separate the AEM hosts from a large asset list first.

```
python3 aem_discoverer.py --file urls.txt
```

Outcome: probes each URL with AEM fingerprints (bypassing the dispatcher
where possible) and reports which hosts are AEM webapps — the natural
filter step before running `aem_hacker.py` against the survivors.
