---
name: "barmie"
categories:
  - blackarch-scanner
version: "1.01"
homepage: "https://github.com/NickstaDB/BaRMIe"
packaged-by: [blackarch]
blackarch-builddate: "2019-07-06"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "NickstaDB/BaRMIe"
github-stars: 750
github-pushed: "2017-09-28"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# barmie

> BaRMIe — Java RMI enumeration and attack tool: lists the objects bound to an RMI registry, matches them against known attack techniques, and launches insecure-method and deserialization attacks from a menu.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Giving Java RMI (Remote Method Invocation) services the assessment attention they usually escape. RMI registries (default port 1099) frequently expose dangerous remote methods with no authentication, but generic scanners cannot speak the RMI protocol. BaRMIe, by Nicky Bloor, enumerates the objects bound to a registry with their interfaces, then either reports the attacks that apply (`-enum`, the default mode) or launches them (`-attack`): calling insecure methods directly, abusing methods that take arbitrary `Object` parameters for deserialization payloads, and injecting deserialization payloads through TCP proxies into otherwise type-mismatched method calls. Attack lines carry a +/- reliability code (application-specific vs JRE-level, auth state, dependency needs). It targets RMI specifically — not JNDI/LDAP endpoints (use other tooling for those).

## Typical use cases

### Enumerate an RMI registry

```
barmie -enum 198.51.100.10 1099
```

Outcome: connects to the registry, lists the bound remote objects with their class/interface names, and prints the known attacks affecting the endpoint with their reliability markers — the triage view that tells you whether `-attack` is worth running. `-enum` is the default mode; the raw form is `java -jar BaRMIe.jar -enum 198.51.100.10 1099`.

### Enumerate and attack

```
barmie -attack 198.51.100.10 1099
```

Outcome: runs the enumeration, then presents a menu of the attacks that apply — insecure method invocation, deserialization via Object parameters, illegal method invocation through the proxy — to launch interactively against the service.

### Sweep RMI services found by nmap

```
barmie --targets nmap-scan.xml
```

Outcome: parses the nmap output (all nmap output formats are supported), picks out every port 1099 / `rmiregistry` / `Java RMI` service it contains, and enumerates each one in turn — batch triage over a large scan without hand-transcribing host/port pairs. A plain text file with one `host port` pair per line works the same way; `--threads` (default 10) and `--timeout` (default 5000 ms) tune the sweep.
