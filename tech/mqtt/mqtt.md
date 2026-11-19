---
technology: "MQTT"
kind: overview
audience: offensive-security
family: network-services
layer: "publish/subscribe message broker (TCP/1883 plaintext, TCP/8883 TLS)"
standards: "OASIS MQTT 3.1.1 (2014) and MQTT 5.0 (2019); brokers: Eclipse Mosquitto, EMQX, HiveMQ, RabbitMQ MQTT plugin"
homepage: https://mqtt.org
status: thin
provenance: "corpus: 2 writeups under the command-context gate (2026-11-12; markers mosquitto_sub/mqttui/1883/tcp/mqtt-subscribe/$SYS) — one file (anonymous broker, retained-message NTLM-coercion chain) and another (localhost-bound mosquitto, SSH -R, $SYS key leak). The structure-pass count of 4 was inflated: two rejected candidates matched only a MIME boundary containing '1883' and a timestamp '.188361' respectively. Upstream-verified: nmap mqtt-subscribe NSE docs; mosquitto.conf(5) (allow_anonymous defaults incl. the 1.6.x flip); OASIS MQTT 3.1.1 and 5.0 specification PDFs (CONNACK Table 3.1, reason-code table, SUBACK codes, section 4.12 Enhanced authentication). Enrichment pass 1, wave 4. Supplement (2026-11-13) rewritten from HackTricks (src/network-services-pentesting/1883-pentesting-mqtt-mosquitto.md), CC BY-NC 4.0, accessed 2026-11-13 (ClientId session attacks, $SYS wildcard rule, Sparkplug B, MQTT-over-WebSocket — see the methodology supplement for verification detail)."
---

# MQTT

> A broker protocol whose whole security model is "the CONNECT packet may optionally carry a username" — on an anonymous broker, one wildcard subscription taps every message in the estate, and one retained publish rewrites whatever automation consumes those messages.

## What it is

MQTT is a lightweight publish/subscribe protocol standardized at OASIS, born out of 1999-era IBM work on telemetry over satellite links and now the default message fabric of IoT, home automation, and OT-adjacent networks. Every client (sensor, gateway, backend service) connects to a central **broker** and publishes messages to a hierarchical **topic** (`site/systems/node/sensor/temperature`); anyone subscribed to a matching **topic filter** receives them. The two wildcards are the entire access question: `#` matches any number of levels (`$SYS/#` = everything under $SYS), `+` matches one level. Clients are identified by a `client id` string they choose themselves.

The protocol's authentication surface is one packet: **CONNECT** carries the client id, keepalive, optional will, and — only if the corresponding flag bits are set — a username and password. What happens next depends entirely on broker configuration and dialect. In MQTT 3.1.1 the server answers CONNACK with one of five return codes (Table 3.1 of the spec: 0 accepted, 4 bad username or password, 5 not authorized) — or simply closes the TCP connection, which the spec permits when no code applies. MQTT 5.0 replaces this with granular **reason codes** (0x86 bad username/password, 0x87 not authorized, 0x8A banned, 0x8C bad authentication method) that travel not just in CONNACK but in PUBACK/SUBACK/UNSUBACK/DISCONNECT — meaning a 5.0 broker tells you *which operation* was denied, while a 3.1.1 broker answers a denied SUBSCRIBE only with SUBACK 0x80 "Failure" and no detail. MQTT 5.0 also adds optional **enhanced authentication** (spec section 4.12): a challenge/response AUTH-packet exchange (e.g. SCRAM) negotiated by an Authentication Method field — rarely deployed, but its presence in a CAPA-like probe distinguishes a 5.0-capable broker immediately.

The operator takeaway: brokers are routinely deployed with **anonymous access and full wildcard read**. The observed record shows both canonical outcomes — an internet-reachable anonymous broker whose `$SYS` tree and topic names leaked internal hostnames and whose retained health-check topic was republished with an attacker URL to coerce Windows NTLM authentication (one observed chain), and a localhost-only mosquitto whose `$SYS/#` subscription dumped an SSH private key a service had published to a topic (the other). Mosquitto's own history makes the anonymous pattern a default to expect: per `mosquitto.conf(5)`, **allow_anonymous defaulted to true in 1.6.x and earlier unless another security option was set**; from 2.0 it defaults to false — but flips back to true-with-localhost-only when no listener is configured. So a 1.x-era broker on the wire is anonymous until proven otherwise, and a 2.x one reachable remotely means someone explicitly configured it.

## The anatomy that matters on an engagement

    CONNECT  (client id, keepalive, [username flag, password flag], will, protocol level 3|4|5)
      ← CONNACK (3.1.1: return code 0-5 or bare TCP close / 5.0: reason code 0x00, 0x86, 0x87, 0x8A...)
    SUBSCRIBE "#" or "$SYS/#"     ← SUBACK (per-filter 0x00-0x02 granted, 0x80 failure in 3.1.1; 0x87 in 5.0)
      ← PUBLISH (retained messages replay immediately; live traffic as it happens)
    PUBLISH -r <topic> <payload>  ← poison the last-known-good state every consumer reads
    $SYS/broker/...               ← broker introspection: version, clients, subscriptions, per-client status

Three structural properties drive most findings:

- **Authentication is optional and coarse.** There is no per-topic capability in the protocol; topic ACLs (mosquitto `password_file` + ACL `topic`/`pattern` lines) are broker config, applied after an otherwise successful CONNECT. A broker that accepts anonymous CONNECTs usually grants subscribe-everything.
- **Retained messages are a free state dump.** Every retained topic replays its last payload to any new subscriber — with zero live traffic you still harvest the estate's last-known configuration, node inventory, and health-check URLs. nmap's mqtt-subscribe script builds its entire report out of this.
- **Publish rights are code execution in whatever consumes the topic.** Services that read commands, health-check URLs, or config from MQTT trust the broker's delivery; a retained PUBLISH with your payload is indistinguishable from the real publisher's last message. In the field this converted a read-only broker into an NTLM-coercion primitive (responder/ntlmrelayx chain).

## Where you'll meet it

- **IoT/OT and building automation** — sensor meshes and SCADA-adjacent gateways; anonymous brokers exposing node telemetry, internal hostnames, and sometimes raw credentials in topics.
- **Internal dev/ops backbones** — startups and SaaS using MQTT (EMQX, HiveMQ, Mosquitto, RabbitMQ's MQTT plugin) for event pipelines; topic trees mirror the org chart and internal DNS.
- **C2 and red-team infrastructure** — MQTT is a documented command-and-control transport (lightweight, firewall-friendly); when you find a broker, ask whether a client in the topic tree is an implant.
- **Localhost-only brokers** on compromised hosts — the service binds 127.0.0.1:1883 (an observed mosquitto 1.4.15) and is reachable only after pivoting (SSH remote forward); `$SYS/#` there yielded a private key.

## Reading the protocol's mind

1. **CONNECT flags decide the auth question before you send credentials**: User Name Flag (bit 7) and Password Flag (bit 6); a password without a username is a protocol violation in 3.1.1. The protocol-level byte (4 = 3.1.1, 5 = 5.0) must match what the listener accepts (mosquitto `accept_protocol_versions`).
2. **CONNACK behavior is a dialect fingerprint**: code 4/5 in 3.1.1, 0x86/0x87/0x8A in 5.0, or a silent TCP close — three distinguishable answers that also tell you whether username guessing is even meaningful.
3. **`$SYS/` is broker introspection by convention**: `$SYS/broker/version`, clients/connected, subscriptions/count, and (as observed) per-client status records exposing usernames, client ids, IPs — free enumeration of every actor on the bus.
4. **Retained-vs-live is a timing oracle**: the retained set arrives instantly on subscribe; anything else is live traffic. An empty-but-instant replay means you hold the full current state.
5. **MQTT 5.0 reason codes leak policy**: SUBACK 0x87 "not authorized" vs 3.1.1's bare 0x80 tells you an ACL denied the topic specifically — useful for mapping which topics are guarded. See the [[mqtt-bug-bounty|methodology]] for the exact probes.

## Where to go next

[[mqtt-bug-bounty|MQTT bug bounty methodology]] — anonymous-connect triage, mqtt-subscribe/mosquitto_sub/mqttui enumeration, retained-message mining, and the retained-poison → responder/ntlmrelayx coercion chain.

## Supplement (2026-11-13): four surfaces beyond the plain broker

Rewritten from HackTricks (src/network-services-pentesting/1883-pentesting-mqtt-mosquitto.md), CC BY-NC 4.0, accessed 2026-11-13; details and commands live in the [[mqtt-bug-bounty|methodology supplement]].

1. **The client id is an attack surface of its own.** Persistent sessions are keyed by a string the client chooses; two open Mosquitto 2.1.2 issues (#3551, #3553) turn a known victim ClientId into unauthenticated session destruction and — for an authenticated low-privilege user — delivery of the victim's queued QoS 1/2 messages from ACL-forbidden topics. IDs come from firmware, app bundles, and serial/MAC conventions.
2. **`#` and `+` never match `$`-prefixed topics** — the $SYS tree must be subscribed as `$SYS/#` explicitly (protocol matching rule, not an ACL bypass; also single-quote `$` topics in shells).
3. **Sparkplug B rides MQTT into OT**: the `spBv1.0/{group}/{type}/{edge}/{device}` namespace plus protobuf metrics gives passive plant-inventory recon and a rich protocol-aware fuzzing surface (lifecycle violations, datatype flips, alias rebinding).
4. **The broker also lives behind WebSockets** on web properties — `wss://host/path` MQTT endpoints with credentials in JS bundles, and per-user topic-name-only isolation (`client/+/chat_session`) that wildcard broadening turns into a cross-tenant tap.
