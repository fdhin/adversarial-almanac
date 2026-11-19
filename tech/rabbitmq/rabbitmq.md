---
technology: "RabbitMQ / AMQP"
kind: overview
audience: offensive-security
family: messaging
status: "thin (2 writeup files pass the re-gated command-context gate — the structure-pass 6 was PID/hex/timestamp collision noise; upstream carries the management surface)"
layer: message broker — AMQP 0-9-1 on 5672/tcp, management HTTP API on 15672/tcp, Erlang distribution on 25672/tcp (epmd on 4369)
language: Erlang
homepage: "https://www.rabbitmq.com/"
github-repo: "rabbitmq/rabbitmq-server"
provenance: "corpus-grounded THIN (re-gated 2026-11-12: 2 writeup files; structure-pass 6 was inflated — four candidates were PID/hex/timestamp collisions or name-only matches, all out; see internal coverage ledger; exemplars: one file (root cron runs amqp-com; tshark captures AMQP on 5672 → cleartext subscriber creds; pika PlainCredentials publish to exchange plugin_data → privileged Lua consumer executes it → root), the other (nmap 5672 banner 'RabbitMQ 3.8.9', distribution port 25672 observed)) + upstream-verified: rabbitmq.com/docs/access-control (default user host-local only — 'Remote connections that use the default user will be refused'), rabbitmq.com/docs/networking (5672 on all interfaces by default), RabbitMQ management-plugin docs (15672 HTTP API: /api/overview, /api/queues, /api/users), RabbitMQ clustering docs (Erlang cookie as inter-node shared secret), 2026-11-12."
---

# RabbitMQ / AMQP

> The broker that authenticates in cleartext on 5672 by default and ships a management API that will tell you every queue, exchange, and user — if you can get past a default account that is deliberately locked to localhost.

## What it is

RabbitMQ is the most-deployed AMQP broker: an Erlang server where producers publish to **exchanges**, bindings route copies into **queues**, and consumers pull — all namespaced inside **virtual hosts (vhosts)** with per-user, per-vhost permissions. Clients speak AMQP 0-9-1 on **5672/tcp** (PLAINSASL auth, cleartext unless TLS is configured), while the optional management plugin exposes a full HTTP API and UI on **15672/tcp**. Inter-node traffic uses the Erlang distribution protocol on **25672/tcp** (with epmd on 4369), authenticated by a shared-secret **Erlang cookie** file — the same cookie that doubles as a full node-takeover token when readable.

The default posture has two halves. A fresh broker is created with the **default user `guest`/`guest`** — but that account is deliberately restricted: the official access-control docs state it "can only be used for host-local connections by default; remote connections that use the default user will be refused" (the `loopback_users` gating). So the famous pair does not work over the network — unless an operator relaxed it, which happens — but **any local foothold turns it back on**: from the host itself, `guest:guest` drives the entire management API. The second half: the data plane on 5672 listens on **all interfaces by default**, and its handshake is cleartext — the field's core chain is exactly that: capture AMQP on the wire, read credentials out of the follow-stream, then publish as a legitimate user (the observed chain).

## The anatomy that matters on an engagement

    port scan → 4369 (epmd), 5672 (amqp), 15672 (mgmt), 25672 (dist)
      → 15672 mgmt API: default guest:guest?  → refused remotely, works from localhost
      → 5672 AMQP: PLAIN auth in cleartext (no TLS by default)
      → capture handshake (tshark port 5672) → usernames/passwords in the stream
      → authenticate as a real user → enumerate vhosts/exchanges/queues
      → publish to an exchange → the privileged consumer executes it   ← observed chain: root-run Lua plugin loader
      → Erlang cookie readable (/var/lib/rabbitmq/.erlang.cookie) → distribution takeover on 25672

Three structural properties drive most findings:

- **The broker trusts its publishers.** A queue's consumer — a service account, a cron job, a plugin loader — runs with whatever privilege the consuming process has; publishing to the right exchange is code execution by proxy (observed: a root-run `amqp-com` fetched and ran whatever URL arrived on `plugin_data`).
- **Credentials cross 5672 in the clear by default.** PLAIN SASL sends username/password unencrypted inside the handshake; an on-host capture (wireshark-group members can run dumpcap/tshark without root — the observed pivot) or an on-path position yields working broker logins directly.
- **The management API is a complete map.** `/api/overview`, `/api/queues`, `/api/exchanges`, `/api/users`, `/api/vhosts` — one authenticated request set replaces hours of guessing the message topology; from a local shell, `guest:guest` + `curl 127.0.0.1:15672` is that map, and `rabbitmqctl` is its CLI equivalent.

## Where you'll meet it

- **Microservice and job-processing backends** — order/fulfillment pipelines, mailers, cron-driven integrations; the consumers are often privileged services, which is what makes a publish primitive escalate (an observed root cron `systemctl start amqp-com` every minute).
- **Internal broker next to your web-shell** — 5672/15672 bound internally, `guest` locked to loopback exactly where your foothold already is; enumerate from localhost rather than across the network.
- **Polyglot stacks** — one broker carrying AMQP, MQTT (1883), STOMP (61613) listeners; each protocol is another cleartext handshake with the same user database behind it.
- **Erlang host details** — epmd on 4369 names every registered node; `rabbit@hostname` in mnesia paths (`/var/lib/rabbitmq/mnesia/rabbit@host`) leaks the node/hostname convention for cookie attacks.

## Reading the broker's mind

1. **`nmap -sV -p5672` fingerprints cleanly** — `5672/tcp open amqp RabbitMQ 3.7.8 (0-9)` (field-observed); the version frames the era, and 4369/25672 alongside confirm the Erlang node.
2. **`guest:guest` fails remotely but succeeds from the host** — that asymmetry is the design, not a mistype; any local execution (web RCE, SSH session) reopens the default account against 15672 (upstream: access-control docs).
3. **PLAIN auth on 5672 is cleartext** — follow the TCP stream of any captured handshake and the credentials are right there (observed: one chain read subscriber emails + passwords out of the AMQP stream).
4. **Publishing is privilege inheritance** — before cracking further, ask *what consumes this exchange*; a root/unit-privileged consumer turns a broker login into host execution with one `basic_publish` (observed: `pika` publish of a URL to `plugin_data` → root).
5. **`.erlang.cookie` is the node's root password** — readable by service-user compromise, it authenticates you to the distribution port as a peer node (upstream: clustering docs) — full broker takeover, all vhosts, all queues.

## Where to go next

[[rabbitmq-bug-bounty]] — the phased agent methodology: port fingerprinting, the guest/localhost fork, management-API and rabbitmqctl enumeration, cleartext capture patterns, publish-to-privileged-consumer chains, and Erlang cookie takeover.
