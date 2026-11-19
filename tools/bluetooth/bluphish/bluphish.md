---
name: "bluphish"
categories:
  - blackarch-bluetooth
version: "9.a7200bd"
homepage: "https://github.com/olivo/BluPhish"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: Apache2
meta-checked: "2026-11-09"
github-repo: "olivo/BluPhish"
github-stars: 16
github-pushed: "2015-12-15"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# bluphish

> Java Bluetooth survey tool that discovers nearby devices and services and reports which services accept connections without pairing.

## What it's for

BluPhish enumerates every Bluetooth device in range, walks each device's service records (SPP, L2CAP, OBEX and friends), attempts to connect to each discovered service, and reports whether the service required pairing — the check that surfaces services accepting unauthenticated connections. By default it tries to pair with everything it probes, which triggers a pairing notification on the target; pass `--no-pair` to keep the survey passive from the target's point of view. It is a Java application on the BlueCove stack, developed against the Windows Bluetooth stack, so treat it as a laboratory survey tool for older hardware rather than a modern bluez workflow.

## Typical use cases

### Discover devices and services, probing pairing requirements

Active service survey of everything in radio range.

```
java -cp .:bluecove-2.1.1-SNAPSHOT.jar BluPhish
```

Outcome: one line per discovered service in the form `Device Name | Service Name | Connection URL | Requires Pairing` — the connection URL (e.g. `btgoep://D896959D35B9:13;authenticate=false;encrypt=false`) shows the transport, device address, port and authentication flags. A "Requires Pairing" value of Yes marks services that refused an unpaired connection; expect pairing prompts on the probed devices.

### Non-pairing discovery pass

Run the same survey without sending pairing attempts to the targets.

```
java -cp .:bluecove-2.1.1-SNAPSHOT.jar BluPhish --no-pair
```

Outcome: the same device, service and connection-URL listing, but no pairing notifications are sent to discovered devices; the pairing column is not meaningful in this mode.

*No corpus evidence — derived from upstream documentation.*
