---
tool: "http-request-smuggler"
build-system: "java"
upstream: "https://github.com/portswigger/http-request-smuggler"
recipe: "upstream"
license: "see repository"
---

# Installing http-request-smuggler

> HTTP Request Smuggler — Burp Suite extension that automatically detects and exploits HTTP request smuggling vulnerabilities.

Source: pentest-book feed; upstream [`portswigger/http-request-smuggler`](https://github.com/portswigger/http-request-smuggler). Background: [HTTP Desync Attacks](https://portswigger.net/blog/http-desync-attacks) and the [request smuggling learning materials](https://portswigger.net/web-security/request-smuggling) with free online labs.

## From the BApp Store (easiest)

```
Burp: Extender -> BApp Store -> install "HTTP Request Smuggler"
```

Outcome: the extension loads into Burp Suite DAST, Professional or Community; a right-click "Launch Smuggle probe" entry appears on requests. (Quoted from upstream: "The easiest way to install this is in Burp Suite, via Extender -> BApp Store.")

## Manual build and JAR load

Turbo Intruder is a build dependency — place its JAR in the source tree first.

```bash
# 1. build Turbo Intruder (see its install notes) -> turbo-intruder-all.jar
git clone https://github.com/portswigger/http-request-smuggler.git
cd http-request-smuggler
cp /path/to/turbo-intruder-all.jar .
./gradlew build fatjar        # Windows: gradlew.bat build fatjar
# 2. load build/libs/http-request-smuggler-all.jar in Burp:
#    Extender -> Extensions -> Add -> Extension type: Java -> select the JAR
```

Outcome: `./gradlew build fatjar` compiles the extension into `build/libs/http-request-smuggler-all.jar`; loading that JAR via `Extender -> Extensions -> Add` installs it without the BApp Store. (Upstream also notes the fatjar output may be named `desynchronize-all.jar` in the current tree — use the `-all.jar` produced by your build.)

## Notes

- No distro packages this extension (checked 2026-11-13); it only runs inside Burp Suite.
- Practice targets: the free PortSwigger request-smuggling labs walk through solving the basic CL.TE case with this extension.

Backlinks: [[webapp]] · [[http-request-smuggler]]
