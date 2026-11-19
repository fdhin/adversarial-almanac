---
name: "jmsdigger-bin"
gentoo-category: "net-analyzer"
version: "20130815"
homepage: "https://github.com/OpenSecurityResearch/jmsdigger"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: prebuilt
license: Apache-2.0
meta-checked: "2026-11-09"
github-repo: "OpenSecurityResearch/jmsdigger"
github-stars: 30
github-pushed: "2014-08-06"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# jmsdigger-bin

> JMSDigger: a Java/Swing GUI for auditing JMS brokers, focused on ActiveMQ.

## What it's for

JMSDigger is an Enterprise Messaging Application assessment tool by Gursev Singh
Kalra (McAfee Foundstone Professional Services, 2014), packaged here as the prebuilt
Java application. Its current release focuses on Apache ActiveMQ: after pointing it
at a broker's JMS URL with credentials, it can test authentication behavior, browse
and dump destination (queue and topic) contents — the classic check for credentials
or PII leaking through misconfigured queues — and enumerate and manipulate durable
subscribers on the broker. The implementation (source packages under
`com.mcafee`) is a Maven-built Java project whose entry point is the Swing GUI
`com.mcafee.gui.JmsDiggerFrame`; there is no command-line interface, so this note is
description-only — launch the packaged jar (e.g. `java -jar` on the jar shipped by
the package) and work through the GUI tabs (connection/authentication, dump
destination, durable subscribers). The tool predates modern ActiveMQ versions and
JMS 2.0 features; against current brokers it serves mainly as a quick authenticated
queue inspector, and the 2014 upstream has not changed since.

*No corpus evidence — description-only note (GUI application, no documented CLI); derived from the upstream README.txt and the packaged source layout.*
