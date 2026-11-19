---
name: "sulley"
categories:
  - blackarch-fuzzer
version: "1.0.bff0dd1"
homepage: "https://github.com/OpenRCE/sulley/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "OpenRCE/sulley"
github-stars: 1454
github-pushed: "2020-12-29"
github-archived: false
github-issues: 56
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sulley

> The classic block-based, session-aware Python fuzzing framework by Pedram Amini, Aaron Portnoy and Ryan Sears (OpenRCE).

## What it's for

Sulley is a fuzzing engine and framework that couples a block-based data-generation language (primitives, sizes, checksums, dependencies) with network capture, target health monitoring and VM snapshot recovery, so unattended fuzzing campaigns can run for days and log every test case. A campaign is orchestrated from a small Python session script that describes the protocol as request blocks chained into a state machine, while separate agent processes watch the wire (one pcap per test case), the target process (crash bins) and the VM (snapshot revert). The framework is Python 2 only and has been unmaintained for years — its own README points new work to its successor boofuzz — and it ships as framework scripts run from the installed share directory rather than as wrapped commands.

## Typical use cases

### Record a pcap of every transmitted test case

Start the network monitor agent on the fuzzing host before a campaign.

```
cd /usr/share/sulley && python2 network_monitor.py -d eth0 -f "src or dst port 5168" -P audits/
```

Outcome: binds a PedRPC agent that captures traffic on eth0 matching the BPF filter and writes one pcap per transmitted fuzz request into `audits/`, giving the exact bytes that preceded any detected fault.

### Capture target crashes on a Windows process

```
cd /usr/share/sulley && python2 process_monitor.py -c audits/target.crashbin -p target.exe
```

Outcome: watches the process named `target.exe`, records every crash with its register context into the crashbin file, and restarts the target between faults under the session script's control.

### Capture crashes on a UNIX target

```
cd /usr/share/sulley && python2 process_monitor_unix.py -c audits/target.crashbin
```

Outcome: the same PedRPC-driven crash capturing for a UNIX process, usable when the target runs on the fuzzing host itself.

### Auto-revert the target VM after each fault

```
cd /usr/share/sulley && python2 vmcontrol.py -r /path/to/vmrun -x /path/to/target.vmx -s "fuzzing snapshot"
```

Outcome: exposes VM control over PedRPC so the session script can revert the target to the named known-good snapshot after every detected fault; add `--vbox` and point `-r` at VBoxManage for VirtualBox images.

### Drive a full campaign from a session script

Describe the protocol with the block API and let the framework walk it.

```python
from sulley import *
from requests import http

sess = sessions.session(session_filename="audits/web.session")
target = sessions.target("198.51.100.10", 80)
target.procmon = pedrpc.client("198.51.100.10", 26002)
sess.add_target(target)
sess.connect(sess.root, s_get("HTTP VERBS"))
sess.fuzz()
```

Outcome: transmits every mutation derived from the bundled HTTP request graph (plus any request you define with `s_initialize`), serializes progress to the session file so interrupted runs resume, and coordinates the agents above to catch faults without attendance.

*No corpus evidence — derived from upstream documentation.*
