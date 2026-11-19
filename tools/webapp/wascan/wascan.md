---
name: "wascan"
categories:
  - blackarch-webapp
version: "37.6926338"
homepage: "https://github.com/m4ll0k/WAScan"
packaged-by: [blackarch]
blackarch-builddate: "2020-01-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "m4ll0k/WAScan"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# wascan

> Python 2 web application scanner that fingerprints, attacks, audits, brute-forces, and mines disclosures through five scan modes.

## What it's for

WAScan (Web Application Scanner, by m4ll0k) is a 2018-era black-box fuzzer: it extracts links and forms from the deployed application, sends payloads, and watches responses for error messages and vulnerable behavior. Its five scan phases cover fingerprinting (CMS, web frameworks, cookies/header security, languages, OS, server, 50+ WAFs), attacks (SQL/blind-SQL injection, XSS, HTML/LDAP/XPath/PHP-code injection, LFI, OS commanding, XXE, buffer overflow, CRLF, header injections), audits (Apache status page, open redirect, PHPInfo, robots.txt, XST), brute-force (admin panels, backdoors, backup dirs/files, common dirs/files, hidden parameters), and disclosure (emails, credit-card patterns, private IPs, fatal errors, SSNs). It is written in Python 2.7 and its upstream repository has been deleted — the tool still runs from the packaged copy but is unmaintained and its payloads predate modern frameworks.

## Typical use cases

### Fingerprint the target stack

Identify what the application runs before attacking it.

```
wascan --url http://example.com/ --scan 0
```

Outcome: prints a fingerprint summary — CMS, web framework, WAF, language, operating system, and server — gathered from passive response analysis.

### Attack a parameterized page

Run the injection battery against a URL with parameters.

```
wascan --url http://example.com/page.php?id=1 --scan 1
```

Outcome: sends SQL, XSS, LFI/RFI, LDAP, XPath, XXE, and command-injection payloads at the parameter and reports each finding with the payload that triggered it.

### Audit and disclosure passes

Check for common exposures and leaky content.

```
wascan --url http://example.com/ --scan 2
```

Outcome: probes the Apache status page, open redirects, PHPInfo, robots.txt, and XST. `--scan 4` instead mines disclosures — emails, private IPs, credit-card patterns, and fatal error messages — from the responses.

### Run the full pipeline

Everything in one invocation.

```
wascan --url http://example.com/ --scan 5
```

Outcome: executes fingerprint, attack, audit, brute-force, and disclosure phases in sequence and prints consolidated findings.

### Brute-force hidden parameters

Discover parameter names the application accepts but does not expose.

```
wascan --url http://example.com/test.php --brute
```

Outcome: guesses parameter names against the target and reports those that change the application's behavior.

### Scan authenticated, through a proxy

Reach protected pages and keep traffic in Burp.

```
wascan --url http://example.com/test.php --scan 5 --auth admin:Password1 --proxy 127.0.0.1:8080
```

Outcome: authenticates with the given Basic credentials and routes every request through the proxy; `--proxy-auth root:Password1` adds credentials for the proxy itself and `--ragent` rotates the User-Agent.

*No corpus evidence — derived from the archived upstream README (the GitHub repository has been deleted).*
