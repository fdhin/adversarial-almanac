---
technology: "IBM MQ"
kind: overview
audience: offensive-security
status: upstream-only
family: messaging
layer: message middleware
homepage: https://www.ibm.com/products/ibm-mq
github-repo: https://github.com/ibm-messaging/mq-container
provenance: "upstream-only; zero corpus evidence by design (owner-approved new-unit wave 2026-11-13) — object/CHLAUTH/PCF model, reason code 2035, MQCMD_CREATE_SERVICE warning text and REST/MQSC endpoints verified against ibm.com/docs IBM MQ 9.4 documentation and the mq-container developer-config; punch-q usage verified against the sensepost/punch-q README + rewritten from HackTricks (src/network-services-pentesting/1414-pentesting-ibmmq.md), CC BY-NC 4.0, accessed 2026-11-13"
---

# IBM MQ

> Enterprise messaging where you authenticate to a CHANNEL, not a user — and where a channel that maps you to an admin identity is two system queues away from running `/bin/sh` as the queue manager.

## What it is

IBM MQ (formerly WebSphere MQ, lineage back to 1993 MQSeries) is the transaction-grade message middleware under large banks, insurers, and retailers. The unit of administration is the **queue manager**, which owns **queues**, **topics**, and **channels**; applications put and get messages while the manager stores and forwards. For an operator the door that matters is the **`SVRCONN`** (server-connection) channel behind the listener on TCP **1414** (configurable). What a connection can actually do is decided by three layered gates: the selected channel, the **CHLAUTH** rules that map the inbound connection to an MCAUSER identity, and **OAM** (Object Authority Manager) permissions on the target objects. A privileged mapping plus OAM rights equals queue-manager administration — and IBM MQ administration includes the ability to define *services*, which run local programs.

Two more sockets complete the picture: with `mqweb` enabled, the **Console and REST APIs** on HTTPS **9443** (administrative REST that executes MQSC, and a messaging REST that browses/puts/deletes messages), and the container image's optional Prometheus metrics on **9157**. The same administrative surface is reachable over PCF (Programmable Command Formats — the wire protocol punch-q and pymqi speak), over MQSC at a terminal, or over the REST admin endpoint: three transports into one authority model.

The operator takeaway: the RCE is not a memory-corruption bug — it is a documented administrative command (`MQCMD_CREATE_SERVICE` / `DEFINE SERVICE`) whose `StartCommand` points at any local binary, with an explicit IBM warning that a user granted it can run arbitrary commands with queue-manager authority. Everything on an engagement is therefore a search for an identity that reaches that authority: default channels, weak CHLAUTH mappings, reused credentials, or a console token.

## The anatomy that matters on an engagement

    client → 1414 listener → SVRCONN channel
      gates: channel exists? → CHLAUTH rule maps you → MCAUSER identity → OAM rights
      failure mode: reason code 2035 (not authorized) — a channel/authz wall, not a bad password
    admin paths (any one suffices):
      PCF  → put command to SYSTEM.ADMIN.COMMAND.QUEUE + dynamic reply queue from
             SYSTEM.DEFAULT.MODEL.QUEUE  → MQCMD_* incl. MQCMD_INQUIRE_*, MQCMD_CREATE_SERVICE
      MQSC → runmqsc on the host, or
      REST → POST https://host:9443/ibmmq/rest/v3/admin/action/qmgr/<qmgr>/mqsc  (plain-text MQSC)
    mqweb roles: MQWebAdmin/MQWebAdminRO = administrative REST; MQWebUser + OAM = messaging REST
      login can be HTTP Basic or token (LtpaToken2 cookie, 120-minute lifetime by default)
    messaging REST (9443, any MQWebUser):
      GET/DELETE/POST /ibmmq/rest/v3/messaging/qmgr/<qmgr>/queue/<q>/message — browse/steal/inject
      POST .../topic/<string>/message — publish

Structural properties that drive findings: (1) channel-first authentication — enumerate channels before users; (2) the split-brain port posture — 1414 ACLed to app subnets while 9443 is reachable from jump hosts, VPN, and ingress; (3) administration is uniform — every admin transport lands in the same authority model, so the weakest one wins.

## Where you'll meet it

- **Integration layers in finance/insurance/retail** — legacy queues named after business processes, service accounts reused across MQ and the OS or LDAP directory behind it.
- **Containerized and Kubernetes deployments** of the developer image (`icr.io/ibm-messaging/mq`) — convenience channels like `DEV.ADMIN.SVRCONN` and weak `app`/`admin` secrets escaping into production-adjacent environments.
- **Web-console-managed estates** — teams that administer through 9443 and forget the REST roles; a dead `/admin/` path does not mean `/messaging/` is absent (from 9.4.0 a stand-alone web server fronts remote queue managers with the messaging API only).

## Reading the framework's mind

1. A successful connection is not a login — it is a channel mapping; reason `2035` usually means CHLAUTH or OAM blocked you, and `DISPLAY CHLAUTH(...) MATCH(RUNCHECK)` tells you exactly which rule fires for your address and CLNTUSER.
2. Developer-era objects are doors: `SYSTEM.DEF.SVRCONN`, `SYSTEM.AUTO.SVRCONN`, and every `DEV.*` channel/queue in the container's default config.
3. PCF administration needs only two system objects — put on `SYSTEM.ADMIN.COMMAND.QUEUE`, model `SYSTEM.DEFAULT.MODEL.QUEUE` for the reply — so those AUTHREC rows are the whole admin gate.
4. The console and the queue manager keep separate credential stores and role models (`MQWebAdmin` vs `MQWebUser`); an LtpaToken2 cookie stolen from a browser is admin-until-expiry on 9443.
5. Client artifacts leak the topology: CCDT files (`AMQCLCHL.TAB` or JSON, including HTTPS-hosted ones via `MQCCDTURL=`), `mqclient.ini`, and key repositories (`.kdb`/`.sth`/`.p12`) carry queue-manager names, channels, hosts, ports, and TLS settings.

## Where to go next

The companion methodology: [[ibmmq-bug-bounty]] — enumeration with punch-q/pymqi, CHLAUTH/OAM triage, message tampering through 1414 and 9443, and the service-definition RCE paths. Adjacent protocol units: [[activemq]], [[rabbitmq]].
