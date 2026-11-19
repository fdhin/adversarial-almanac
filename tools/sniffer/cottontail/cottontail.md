---
name: "cottontail"
categories:
  - blackarch-sniffer
version: "93.b7f5222"
homepage: "https://github.com/QKaiser/cottontail"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: python
license: BSD
meta-checked: "2026-11-09"
github-repo: "QKaiser/cottontail"
github-stars: 34
github-pushed: "2021-02-13"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# cottontail

> Silently captures RabbitMQ messages by acting as one more consumer on every queue and exchange.

## What it's for

cottontail (Quentin Kaiser) harvests messages flowing through a RabbitMQ broker you already have credentials for. It enumerates vhosts, exchanges and queues via the rabbitmq_management HTTP API (AMQP itself has no listing mechanism), then opens one process per vhost and binds to every queue and exchange except `amqp.*`, requeueing each message it reads so legitimate consumers still receive it. Coverage depends on the topology: work queues yield a round-robin share of messages, while fanout exchanges (wildcard `#` binding) and direct exchanges (one binding per discovered routing key) deliver everything bound consumers see. It is not an exploit — it requires the management plugin enabled and an account with an `administrator`, `management`, `monitoring` or `policy` profile — and it cannot read vhosts the account lacks permissions for.

## Typical use cases

### Capture all messages on a broker

Watch every reachable queue and exchange with one command.

```
cottontail http://198.51.100.10:15672/ --username user --password Password1
```

Outcome: cottontail queries the management API, spawns one process per vhost, binds to each queue and exchange, and prints every received message to the console; each message is requeued with its full metadata (`reply_to`, `correlation_id`), so real consumers keep working and RPC calls still reach their server.

### Show message properties and headers

Include routing metadata with each captured payload.

```
cottontail http://198.51.100.10:15672/ --username user --password Password1 -v
```

Outcome: verbose mode adds each message's properties and headers to the console output, enough to identify producers, RPC patterns and per-queue message flow.

### Capture via an HTTPS management endpoint

Use when the management API is exposed over TLS.

```
cottontail https://target.example.com:15671/ --username user --password Password1
```

Outcome: the same live message feed, connecting to the management API over HTTPS; if the AMQP listener is unreachable, cottontail falls back to dumping messages through the HTTP API.

*No corpus evidence — derived from upstream documentation.*
