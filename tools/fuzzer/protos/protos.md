---
name: "protos"
gentoo-category: "app-fuzz"
version: "1.1"
homepage: "http://www.ee.oulu.fi/research/ouspg/protos/"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: scripts
license: GPL-2
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# protos

> University of Oulu (OUSPG) test suites: jar-packaged batteries of malformed protocol messages for HTTP, LDAP, SNMPv1, SIP, H.225, ISAKMP, and DNS implementations.

## What it's for

PROTOS was a research project at the University of Oulu's OUSPG group on black-box security testing of protocol implementations. Its deliverables are per-protocol test suites — Java jar files (the c05–c09 series), each packing systematically malformed messages you fire at an implementation to expose parsing flaws. The collection covers HTTP reply parsing, LDAPv3 (application and encoding levels), SNMPv1 (requests and traps, application and encoding), SIP, H.225.0 v4, ISAKMP, and DNS (query, response, zone transfer). There is no upstream code repository; the original ee.oulu.fi pages survive only as archives, and the packaged install drops the jars into /opt/protos behind a `protos` wrapper that launches the right jar with `java -jar`.

## Typical use cases

### List the available suites

```
protos
```

Outcome: prints the wrapper usage — the supported suite names (http-reply, ldapv3-app, ldapv3-enc, snmpv1-req-app, snmpv1-req-enc, snmpv1-trap-app, snmpv1-trap-enc, sip, h2250v4, isakmp, dns-query, dns-response, dns-zonetransfer) — before any traffic is sent.

### Show a suite's own options

```
protos snmpv1-req-app -h
```

Outcome: runs the c06 SNMPv1 request suite with `-h`, printing that jar's specific arguments so a real run against a target can be composed.

### Invoke a suite jar directly

```
java -jar /opt/protos/c07-sip-r2.jar -h
```

Outcome: calls the SIP suite jar exactly as the wrapper does under the hood (`java -jar /opt/protos/<suite>.jar <args>`), the form to use in scripts or on installs without the wrapper.

*No corpus evidence — derived from upstream documentation.*
