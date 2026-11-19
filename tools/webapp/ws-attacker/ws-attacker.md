---
name: "ws-attacker"
categories:
  - blackarch-webapp
version: "1.7"
homepage: "http://ws-attacker.sourceforge.net/"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: custom
meta-checked: "2026-11-09"
github-repo: "RUB-NDS/WS-Attacker"
github-stars: 497
github-pushed: "2024-10-03"
github-archived: false
github-issues: 19
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ws-attacker

> Java framework for SOAP web service penetration testing, built on the SoapUI engine.

## What it's for

WS-Attacker (developed by the Chair for Network and Data Security at Ruhr University Bochum and Hackmanit) is a modular framework for web services (SOAP) penetration testing. It loads WSDL files and sends SOAP messages to service endpoints, and extends that core with attack plugins: SOAPAction spoofing, WS-Addressing spoofing, XML Signature Wrapping, XML-based denial-of-service (including adaptive DoS), and the XML Encryption attacks published by the same group. It is a Java desktop application (GUI built on the underlying SoapUI framework) — WSDL projects and attacks are configured through the interface, not a CLI. The packaged version is 1.7; upstream development continued past it on GitHub.

## Typical use cases

### Launch the testing workbench

```
ws-attacker
```

Outcome: starts the Java GUI; create a project, load the target's WSDL URL to enumerate its operations and requests, then select an attack plugin (e.g. SOAPAction Spoofing or XML Signature Wrapping) on a request and run it against the service.

*No corpus evidence — derived from upstream documentation (README and project pages).*
