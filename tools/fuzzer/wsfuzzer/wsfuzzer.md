---
name: "wsfuzzer"
categories:
  - blackarch-fuzzer
version: "1.9.5"
homepage: "https://www.owasp.org/index.php/Category:OWASP_WSFuzzer_Project"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: LGPL
meta-checked: "2026-11-09"
github-repo: "Seabreg/wsfuzzer"
github-stars: 0
github-pushed: "2019-09-15"
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

# wsfuzzer

> OWASP's SOAP fuzzer for pentesting WSDL-described web services.

## What it's for

WSFuzzer (Andres Andreu / neuroFuzz, with Cosmin Banciu and David Shu, v1.9.5) automates SOAP web-service testing: given a WSDL (or manually supplied host/endpoint/namespace details in no-WSDL mode) it enumerates the service's operations and parameters, builds SOAP requests with attack data injected into the chosen parameters, and reports unusual responses. It supports HTTP basic auth, WS-Security username/password tokens, client certificates, an upstream proxy for inspecting traffic in Burp or similar, and XML output of results. Development ended around 2010, so it targets classic SOAP/ASMX-style services and needs a Python 2 runtime.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Fuzz a WSDL-described service interactively

Point WSFuzzer at a WSDL and pick what to fuzz.

```
python WSFuzzer.py --wsdl http://target.example.com/service?wsdl
```

Outcome: the WSDL is parsed, every discovered method with its parameters is listed, and the tool asks which methods to fuzz (`0,1,2` indices or `A` for all), whether to attack parameters simultaneously, and which attack-data dictionary to use — then sends the fuzzed SOAP requests and summarizes anomalous responses.

### Preload target, credentials and proxy on the command line

Start the session with connection details already set.

```
python WSFuzzer.py --wsdl http://target.example.com/service?wsdl --bauser user --bapass Password1 --proxy 127.0.0.1 --proxyport 8080
```

Outcome: the session opens with the WSDL, basic-auth credentials and an upstream proxy already configured, so every fuzzed request appears in the proxy's history for manual review as the run proceeds. Further overrides include `--host`, `--endpoint`, `--namespace`, `--wsseuser`/`--wssepass`, `--keyfile`/`--certfile`, `--xml` and a full run definition via `--conf <file>`.
